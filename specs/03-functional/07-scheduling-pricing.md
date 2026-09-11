# Đặc tả chức năng 07: Lịch và Dynamic Pricing

## 1. Mục tiêu

Module cấu hình giờ mở cửa, slot, holiday hoặc special event, price rule và pass access rule theo branch và thời gian. Kết quả phải deterministic và có thể giải thích.

## 2. Các khái niệm riêng biệt

| Khái niệm | Mục đích |
|---|---|
| Opening Hours | Branch mở/đóng theo weekday/effective date |
| Swimming Slot | Nhãn/khung vận hành, ví dụ Morning/Evening |
| Price Rule | Thay đổi số tiền khi quote |
| Entitlement Time Rule | Cho/không cho pass sử dụng tại thời điểm access |
| Holiday Calendar | Ngày đặc biệt đã publish cho branch/organization |

Price rule không tự làm pass invalid. Entitlement restriction cũng không tự thay đổi order price nếu không có rule tương ứng.

## 3. Time semantics

- Rule/slot interval dùng `[start,end)` theo branch timezone.
- Slot kéo dài qua nửa đêm phải được biểu diễn bằng hai segment hoặc `end_day_offset=1`.
- Calendar date resolve theo branch, không UTC date.
- Nếu sau này có branch ngoài Việt Nam, timezone library sẽ xử lý DST.
- Rule publish cần preview các boundary exact start/end.

## 4. Slot rules

- `start < end` trong segment.
- Nếu cùng `coverage_group` yêu cầu partition, các slot không được overlap. Slot thuộc group khác có thể overlap.
- Effective date range và weekday required.
- Khi sửa slot đã được reference, hệ thống tạo version hoặc effective change mới và giữ nguyên snapshot cũ của order hoặc pass.

## 5. Price rule model

Mỗi rule có:

- status/version/effective range;
- branch/product/category/channel/member segment scopes;
- conditions: weekday, slot, holiday, date/time range;
- adjustment: `PERCENT` hoặc `FIXED`, sign, value;
- `priority`, `exclusive_group`, `stackable`, `calculation_mode`;
- reason label hiển thị khách hàng.

## 6. Cách tính giá theo thứ tự cố định

1. Load published rules cùng pricing snapshot version.
2. Filter effective time/scope/condition.
3. Trong mỗi `exclusive_group`, chọn rule có priority nhỏ nhất; tie-break by rule ID. Rule không có group được giữ.
4. Sort selected rules theo priority rồi ID.
5. `ADDITIVE` default: phần trăm tính trên base price; cộng fixed amount; tổng hợp cuối.
6. `COMPOUND` chỉ dùng khi price policy cho phép và áp tuần tự theo sort.
7. Apply floor/cap nếu policy có.
8. Round một lần theo currency policy; grand total không âm.

Admin preview phải hiển thị priority và calculation mode. Nếu Business đổi cách tính, nhóm phải cập nhật DEC và regression fixture trước khi publish.

## 7. Quote contract

Quote trả:

```json
{
  "quoteId": "uuid",
  "currency": "VND",
  "baseAmount": 100000,
  "adjustments": [
    {"ruleId": "uuid", "label": "Weekend", "type": "PERCENT", "amount": 10000}
  ],
  "taxAmount": 0,
  "finalAmount": 110000,
  "pricingVersion": "2026-09-01.3",
  "expiresAt": "2026-09-12T10:05:00Z",
  "fingerprint": "opaque"
}
```

Khi tạo order, client gửi `quoteId`. Server không dùng amount do client cung cấp và phải kiểm tra lại expiry cùng fingerprint.

## 8. Pass access rule evaluation

Tại thời gian của server, hệ thống xác định branch timezone, opening hours, branch scope của pass, time rule và exception calendar phù hợp. Deny code phải phân biệt `BRANCH_CLOSED`, `TIME_RESTRICTION` và `HOLIDAY_RESTRICTION`. Paid upgrade hoặc surcharge tùy chọn tại gate nằm ngoài MVP cho tới khi có purchase flow an toàn.

## 9. Publish workflow

1. Manager tạo Draft.
2. Validate overlap/scope/value/range.
3. Preview matrix bằng các sample date có cả boundary và holiday.
4. Compare impact với current published version.
5. Publish có `effective_from` tương lai hoặc permission đặc biệt cho immediate.
6. Audit giá trị old/new, phát config event và invalidate cache bằng version.

## 10. API impacts

- `GET/POST /api/v1/swimming-slots`
- `PATCH /api/v1/swimming-slots/{slotId}`
- `GET/POST /api/v1/pricing-rules`
- `POST /api/v1/pricing-rules/{ruleId}/publication`
- `POST /api/v1/price-quotes`
- `GET/POST /api/v1/holiday-calendars`
- `GET/POST /api/v1/entitlement-time-rules`
- `POST /api/v1/pricing-simulations`

## 11. Errors

`SLOT_OVERLAP`, `RULE_SCOPE_INVALID`, `RULE_PERIOD_INVALID`, `RULE_CONFLICT`, `PRICING_NOT_RESOLVABLE`, `QUOTE_EXPIRED`, `QUOTE_MISMATCH`, `BRANCH_CLOSED`, `TIME_RESTRICTION`, `HOLIDAY_RESTRICTION`, `VERSION_CONFLICT`.

## 12. Acceptance criteria

- `AC-PRC-001`: Exact slot start matches; exact end does not.
- `AC-PRC-002`: Holiday + peak non-stackable cùng group chọn đúng priority, deterministic.
- `AC-PRC-003`: Additive và compound cho cùng fixture cho kết quả expected khác nhau, có breakdown.
- `AC-PRC-004`: Quote tampered amount/fingerprint bị từ chối.
- `AC-PRC-005`: Published future rule không ảnh hưởng order trước effective time.
- `AC-PRC-006`: Cache invalidation failure không làm dùng rule sai quá quote version/freshness guard.
- `AC-PRC-007`: Pass restriction deny không tự phát sinh charge/upgrade.
- `AC-PRC-008`: Rule tie được resolve ổn định theo priority/ID.

## 13. Quyết định baseline

Operations công bố lịch ngày lễ năm kế tiếp trước ngày 01/12. Rule có scope cụ thể hơn được xét trước, sau đó theo priority, effective time và ID. Trong cùng non-stackable group chỉ lấy rule ưu tiên cao nhất. Giá đã gồm thuế và làm tròn đến 1 VND. Member segment được hoàn thiện trong giai đoạn cài đặt; coupon, promotion code và gate surcharge upgrade được chuyển sang giai đoạn sau. Chi tiết truy vết tại `OQ-009` và `OQ-014`.

## Thuật ngữ cần biết

| Thuật ngữ | Giải thích dễ hiểu |
|---|---|
| Dynamic pricing | Cách thay đổi giá theo thời gian, chi nhánh, nhóm khách hoặc điều kiện khác. |
| Slot | Một khung giờ vận hành được đặt tên và cấu hình. |
| Deterministic | Cùng dữ liệu và rule thì hệ thống luôn tính ra cùng kết quả. |
| Priority | Thứ tự dùng để chọn rule nào được xét trước. |
| Fingerprint | Mã đại diện cho nội dung quote, giúp phát hiện dữ liệu đã bị thay đổi. |
