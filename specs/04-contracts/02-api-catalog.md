# API catalog

Catalog là logical contract dùng chung cho frontend, backend và device. Request cùng response schema chi tiết được đưa vào OpenAPI dựa trên các quyết định P0 đã chốt.

## Customer và entitlement

| Method/path | Operation | Permission | Idempotency/concurrency |
|---|---|---|---|
| `GET /members` | Search/list members | `member.read` + scope | cursor |
| `POST /members` | Create member | `member.create` | key required |
| `GET /members/{id}` | Member detail | scoped/self | Không |
| `PATCH /members/{id}` | Update allowed fields | scoped/self | `If-Match` |
| `GET /members/{id}/passes` | Pass list/history | scoped/self | cursor |
| `GET /member-passes/{id}` | Pass detail/ledger summary | scoped/self | Không |
| `GET /member-passes/{id}/eligibility` | Explain eligibility | scoped/self/device-internal | no side effect |
| `POST /member-passes/{id}/adjustments` | Entry/expiry adjustment | `pass.adjust` | key + expected version |
| `POST /member-passes/{id}/cancellations` | Cancel pass | `pass.cancel` | key + expected version |
| `GET/POST /pass-products` | List/create draft | read/configure | key on POST |
| `PATCH /pass-products/{id}` | Edit draft/new version | `product.configure` | `If-Match` |
| `POST /pass-products/{id}/publication` | Publish version | `product.publish` | key + expected version |

## Access và facility

| Method/path | Operation | Permission | Notes |
|---|---|---|---|
| `POST /gate/access-requests` | IN/OUT decision | device auth | key/requestId required |
| `POST /gate/access-events/{id}/acknowledgements` | Physical gate result | same device/gate | idempotent |
| `GET /branches/{id}/capacity` | Current snapshot | `capacity.read` | includes `asOf` |
| `GET /branches/{id}/access-events` | Event list | `access.read` | cursor + scoped |
| `POST /access-events/{id}/overrides` | Linked override | `access.override` | key + reason |
| `POST /access-sessions/{id}/reconciliations` | Close/correct unresolved | `access.reconcile` | key + reason |
| `GET /realtime/capacity` | SSE stream | `capacity.read` | snapshot on reconnect |
| `GET/POST /lockers` | List/create locker | read/configure | scope |
| `PATCH /lockers/{id}` | Edit config | `locker.configure` | `If-Match` |
| `POST /locker-assignments` | Assign | `locker.assign`/internal | key |
| `POST /locker-assignments/{id}/releases` | Release | `locker.release`/internal | key |
| `POST /lockers/{id}/operational-status-changes` | Maintenance/emergency | permission-specific | key + reason |
| `POST /lockers/{id}/emergency-unlocks` | Unlock command | `locker.emergency_unlock` | step-up + key |
| `POST /locker-device-events` | Device callback | gateway auth | provider event dedupe |

### Yêu cầu contract cho đồng bộ ngoại tuyến

Contract đồng bộ từ máy quầy sẽ được chốt trong OpenAPI ở mốc thiết kế tuần 6. Dù chọn truyền từng bản ghi hay theo lô, contract phải mang `requestId`, định danh thiết bị, thời gian quan sát, phiên bản Local Cache và kết quả đã hiển thị tại quầy. Server phải xử lý idempotent, trả trạng thái cho từng bản ghi và chỉ xóa khỏi hàng đợi cục bộ sau khi đã xác nhận nhận dữ liệu.

## Freeze

| Method/path | Operation | Permission | Notes |
|---|---|---|---|
| `GET /freeze-requests` | Work queue/history | scoped/own | cursor |
| `POST /freeze-requests` | Submit request | scoped/own | key |
| `GET /freeze-requests/{id}` | Detail | scoped/own | Không |
| `POST /freeze-requests/{id}/decisions` | Approve/reject | `freeze.approve` | key + expected version |
| `POST /freeze-requests/{id}/cancellations` | Cancel | scoped/own | key + expected version |
| `POST /freeze-periods/{id}/early-resumptions` | Resume early | permission/policy | key + reason |
| `GET /member-passes/{id}/freeze-entitlement` | Preview quota | scoped/own | no side effect |

## Training

| Method/path | Operation | Permission | Notes |
|---|---|---|---|
| `GET/POST /class-definitions` | List/create template | read/configure | key on POST |
| `GET/POST /class-offerings` | List/create offering | read/configure | key on POST |
| `GET/PATCH /class-offerings/{id}` | Detail/update | scoped | `If-Match` on PATCH |
| `POST /class-offerings/{id}/publication` | Publish/open | `class.publish` | key + expected version |
| `GET /class-sessions` | Calendar/search | scoped/own | cursor/time filter |
| `POST /class-offerings/{id}/enrollments` | Enroll/hold seat | scoped/own | key |
| `POST /enrollments/{id}/cancellations` | Cancel | scoped/own | key + policy result |
| `POST /class-offerings/{id}/waitlist-entries` | Join waitlist | scoped/own | key |
| `GET /coaches/{id}/schedule` | Coach schedule | assigned/scoped | time filter |
| `PUT /class-sessions/{id}/attendance` | Replace roster outcomes | assigned/manager | `If-Match` + key |

## Commerce và pricing

| Method/path | Operation | Permission | Notes |
|---|---|---|---|
| `POST /price-quotes` | Resolve sell price | scoped/own | short-lived quote |
| `POST /orders` | Create order from quote | POS/online | key required |
| `GET /orders` | Search orders | scoped/own | cursor |
| `GET /orders/{id}` | Order/payment/fulfillment | scoped/own | Không |
| `POST /orders/{id}/payment-attempts` | Initiate online/QR | scoped/own | key |
| `POST /orders/{id}/cash-payments` | Settle cash | `payment.cash` | key + shift/branch |
| `POST /payments/webhooks/{provider}` | Provider callback | signature | provider dedupe |
| `POST /payments/{id}/refund-requests` | Request refund | scoped/own | key |
| `POST /refund-requests/{id}/decisions` | Approve/reject | `refund.approve` | key + separation rule |
| `GET /orders/{id}/receipt` | Receipt | scoped/own | immutable/versioned |
| `POST /fulfillments/{id}/retries` | Manual retry | `fulfillment.retry` | key |
| `GET/POST /swimming-slots` | Slot list/create | read/configure | key on POST |
| `GET/POST /pricing-rules` | Rule list/create | read/configure | key on POST |
| `POST /pricing-rules/{id}/publication` | Publish | `pricing.publish` | key + expected version |
| `GET/POST /holiday-calendars` | Calendar config | read/configure | versioned |
| `GET/POST /entitlement-time-rules` | Access time rules | read/configure | versioned |
| `POST /pricing-simulations` | Admin preview matrix | `pricing.read` | no business side effect |

## Reporting

| Method/path | Operation | Permission | Notes |
|---|---|---|---|
| `GET /reports/revenue` | Revenue aggregate | `report.revenue.read` | basis/timezone/freshness |
| `GET /reports/traffic` | Check-in/visitor buckets | `report.operations.read` | explicit bucket |
| `GET /reports/capacity` | Occupancy analytics | `report.operations.read` | snapshot/timeline |
| `GET /reports/classes` | Class utilization | `report.class.read` | scoped |
| `POST /report-exports` | Async export job | report-specific export | key |
| `GET /report-exports/{id}` | Job status | creator/scoped | Không |
| `GET /report-exports/{id}/download` | Authorized download | creator/scoped | short-lived response |

## Internal-only application contracts

- `Commerce.fulfill(orderItemId)` → Entitlement/Training/Facility.
- `Access.decide(request)` → Entitlement eligibility + Facility reservation.
- `Reporting.project(event)` → projection tables.
- `Platform.publishOutbox()` và `Platform.deliverNotification()`.

Internal contract không được public qua HTTP, trừ khi module được tách thành service sau này. Dù chạy nội bộ, contract vẫn phải có version và hỗ trợ idempotency.

## Thuật ngữ cần biết

| Thuật ngữ | Giải thích dễ hiểu |
|---|---|
| Logical contract | Bản mô tả endpoint và hành vi cần có, chưa đi sâu vào mọi field của schema. |
| Permission | Quyền cho phép một actor thực hiện một action trên resource. |
| Concurrency | Trường hợp nhiều request cùng đọc hoặc sửa dữ liệu trong một thời điểm. |
| Callback | Request do hệ thống bên ngoài chủ động gửi về SunSwim khi có kết quả. |
| Internal contract | Quy ước gọi chức năng giữa các module bên trong hệ thống. |
