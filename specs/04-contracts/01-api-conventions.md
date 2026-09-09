# Quy ước REST API

## 1. Giao thức và phiên bản

- HTTPS + JSON UTF-8.
- Base path `/api/v1`.
- OpenAPI 3.1.x là machine-readable contract. Nhóm khóa patch version khi bắt đầu implementation.
- Breaking change phải tạo `/v2` hoặc dùng migration contract đã được duyệt. Không được tự đổi nghĩa của field hiện có.
- JSON field dùng `camelCase`; resource URL dùng plural `kebab-case` nếu nhiều từ.

## 2. Resource design

```text
GET    /members/{memberId}
PATCH  /members/{memberId}
GET    /members/{memberId}/passes
POST   /member-passes/{passId}/adjustments
POST   /freeze-requests/{requestId}/decisions
POST   /orders/{orderId}/payment-attempts
```

Command có audit hoặc lifecycle riêng phải được biểu diễn bằng sub-resource như `adjustments`, `decisions`, `cancellations` và `reconciliations`. Không dùng endpoint dạng `/doSomething`.

## 3. Identifiers và scope

- Public ID là một opaque UUID string, vì vậy client không được suy diễn thứ tự.
- Server luôn đối chiếu `branchId` trong request với authenticated scope.
- Self-service lấy subject từ identity token và không tin `memberId` do client tùy ý gửi.
- Branch và gate của device được lấy từ device identity. Giá trị trong body chỉ dùng để cross-check.

## 4. Time và money

- Instant: RFC 3339 có offset, ví dụ `2026-09-10T08:30:00+07:00`; response canonical có thể UTC `Z`.
- Date-only: `YYYY-MM-DD` khi thật sự là ngày nghiệp vụ.
- Interval filter: `from` inclusive, `to` exclusive.
- Response có timezone khi kết quả phụ thuộc lịch branch.
- Amount là JSON integer và currency là ISO code. Calculation contract không dùng float hoặc chuỗi đã format.

## 5. Pagination/filter/sort

Collection lớn dùng cursor pagination theo cấu trúc sau:

```json
{
  "data": [],
  "page": {
    "nextCursor": "opaque-or-null",
    "hasMore": false
  }
}
```

- `limit` default 20, max do endpoint contract định nghĩa.
- Cursor là opaque và được gắn với filter cùng sort; client không được chỉnh sửa cursor.
- Sort whitelist, ví dụ `sort=-createdAt,memberCode`.
- Filter dùng query parameter rõ nghĩa; không nhận raw SQL/filter expression tùy ý.
- Report aggregate có thể dùng page/offset nếu dataset ổn định nhỏ, nhưng catalog phải ghi rõ.

## 6. Idempotency

Mutation tạo side effect tài chính/quyền/access nhận header:

```http
Idempotency-Key: <client-generated-opaque-id>
```

Server lưu key, actor hoặc client namespace, operation, normalized request hash và reference tới response hoặc result.

- Cùng key/cùng payload: trả cùng semantic result.
- Cùng key/khác payload: `409 IDEMPOTENCY_KEY_REUSED`.
- Request đang xử lý: `409 REQUEST_IN_PROGRESS` hoặc poll resource theo contract.
- Provider webhook dedupe bằng provider event/transaction ID, không phụ thuộc header client.
- Retention TTL theo loại operation và legal/operations decision; không xóa sớm hơn retry window.

## 7. Optimistic concurrency

Mutable aggregate trả `ETag` hoặc `version`. Update và decision nhạy cảm phải gửi `If-Match` hoặc `expectedVersion`. Nếu version không khớp, server trả `409 VERSION_CONFLICT` cùng current reference mà người dùng được phép xem.

Database lock vẫn cần để bảo vệ invariant khi có concurrency. ETag không thay thế row lock trong gate, capacity hoặc payment.

## 8. HTTP status

| Status | Dùng cho |
|---:|---|
| 200 | Read/update/command hoàn tất có body |
| 201 | Resource/sub-resource đã tạo |
| 202 | Async job/provider processing accepted |
| 204 | Success không body |
| 400 | Malformed syntax/JSON/query |
| 401 | Thiếu/sai authentication |
| 403 | Authenticated nhưng không có quyền/scope |
| 404 | Resource không tồn tại hoặc được che để chống enumeration |
| 409 | Version/state/idempotency/concurrency conflict |
| 422 | Payload đúng cú pháp nhưng vi phạm validation/business rule |
| 429 | Rate limit |
| 500 | Lỗi không dự kiến, không lộ internals |
| 503 | Dependency/service tạm unavailable; `Retry-After` nếu biết |

Một gate decision hợp lệ về protocol có thể trả HTTP 200 cùng `decision=DENY`. Lỗi authentication, device hoặc protocol vẫn trả 4xx hoặc 5xx.

## 9. Error format

`application/problem+json` theo RFC 9457:

```json
{
  "type": "urn:sunswim:problem:capacity-full",
  "title": "Capacity full",
  "status": 422,
  "detail": "Bể hiện đã đạt sức chứa tối đa.",
  "instance": "/api/v1/gate/access-requests/0199...",
  "code": "CAPACITY_FULL",
  "requestId": "0199...",
  "errors": []
}
```

`detail` không được chứa stack trace, SQL, provider secret hoặc thông tin về resource mà caller không có quyền xem.

## 10. Validation

- Unknown field: reject trong command/payment/device contracts nhạy cảm; read filter không biết cũng reject.
- String trim/Unicode normalization theo field policy; không mutate legal name ngoài trim an toàn.
- Phone normalization cần library/region policy được test, không chỉ regex.
- Server tính price, balance, capacity, role và timestamps authoritative.
- File upload dùng content/type/size allowlist, malware scan và private storage.

## 11. Rate limiting và retry

- Limit theo actor/device/IP/operation, có metric và response headers theo gateway capability.
- Client chỉ retry automatic operation idempotent hoặc có idempotency key.
- Retry 429/503 với exponential backoff + jitter; tôn trọng `Retry-After`.
- Không retry validation/auth error.

## 12. Observability headers

- Client có thể gửi `X-Request-ID`; server validate/generate và echo.
- W3C `traceparent` nếu tracing enabled.
- Correlation ID cho end-to-end order/payment/fulfillment, access/locker.
- Không dùng header làm quyền nếu không được gateway ký/xác thực.

## Thuật ngữ cần biết

| Thuật ngữ | Giải thích dễ hiểu |
|---|---|
| Endpoint | Một địa chỉ API dành cho một loại thao tác cụ thể. |
| Cursor pagination | Cách phân trang bằng một mã đánh dấu vị trí thay vì số trang. |
| Idempotency key | Mã giúp server nhận ra request được gửi lại và tránh tạo tác động trùng. |
| ETag | Giá trị đại diện cho version hiện tại của resource. |
| Rate limiting | Giới hạn số request trong một khoảng thời gian để bảo vệ hệ thống. |
