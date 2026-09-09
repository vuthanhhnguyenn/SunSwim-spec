# Functional Spec 07: Scheduling và Dynamic Pricing

## 1. Mục tiêu

Cấu hình giờ mở cửa, slot, holiday/special event, price rules và pass access rules có hiệu lực theo branch/time một cách deterministic và giải thích được.

## 2. Tách khái niệm

| Khái niệm | Mục đích |
|---|---|
| Opening Hours | Branch mở/đóng theo weekday/effective date |
| Swimming Slot | Nhãn/khung vận hành, ví dụ Morning/Evening |
| Price Rule | Thay đổi số tiền khi quote |
| Entitlement Time Rule | Cho/không cho pass sử dụng tại thời điểm access |
| Holiday Calendar | Ngày đặc biệt đã publish cho branch/organization |

Price rule không tự làm pass invalid; entitlement restriction không tự thay đổi order price nếu không có rule tương ứng.

## 3. Time semantics

- Rule/slot interval dùng `[start,end)` theo branch timezone.
- Slot qua nửa đêm được biểu diễn rõ bằng hai segment hoặc `end_day_offset=1`.
- Calendar date resolve theo branch, không UTC date.
- DST được library timezone xử lý nếu sau này có branch ngoài Việt Nam.
- Rule publish cần preview các boundary exact start/end.

## 4. Slot rules

- `start < end` trong segment.
- Không overlap nếu cùng `coverage_group` yêu cầu partition; overlap khác group được phép.
- Effective date range và weekday required.
- Sửa slot đã referenced tạo version/effective change, không đổi order/pass snapshot cũ.

## 5. Price rule model

Mỗi rule có:

- status/version/effective range;
- branch/product/category/channel/member segment scopes;
- conditions: weekday, slot, holiday, date/time range;
- adjustment: `PERCENT` hoặc `FIXED`, sign, value;
- `priority`, `exclusive_group`, `stackable`, `calculation_mode`;
- reason label hiển thị khách hàng.

## 6. Deterministic evaluation baseline

1. Load published rules cùng pricing snapshot version.
2. Filter effective time/scope/condition.
3. Trong mỗi `exclusive_group`, chọn rule có priority nhỏ nhất; tie-break by rule ID. Rule không có group được giữ.
4. Sort selected rules theo priority rồi ID.
5. `ADDITIVE` default: phần trăm tính trên base price; cộng fixed amount; tổng hợp cuối.
6. `COMPOUND` chỉ dùng khi price policy cho phép và áp tuần tự theo sort.
7. Apply floor/cap nếu policy có.
8. Round một lần theo currency policy; grand total không âm.

Priority/calculation mode phải hiện trong admin preview. Nếu Business muốn semantics khác, cập nhật DEC và regression fixtures trước publish.

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

Create order gửi `quoteId`; server không tin amount client và revalidate expiry/fingerprint.

## 8. Pass access rule evaluation

At server time, resolve branch timezone, opening hours, pass branch scope, applicable time rule và exception calendar. Deny code phân biệt `BRANCH_CLOSED`, `TIME_RESTRICTION`, `HOLIDAY_RESTRICTION`. Optional paid upgrade/surcharge tại gate ngoài MVP cho đến khi có safe purchase flow.

## 9. Publish workflow

1. Manager tạo Draft.
2. Validate overlap/scope/value/range.
3. Preview matrix với sample dates including boundary/holiday.
4. Compare impact với current published version.
5. Publish có `effective_from` tương lai hoặc permission đặc biệt cho immediate.
6. Audit old/new và emit config event; invalidate cache bằng version.

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

Operations công bố lịch ngày lễ năm kế tiếp trước ngày 01/12. Rule có scope cụ thể hơn được xét trước, sau đó theo priority, effective time và ID. Trong cùng non-stackable group chỉ lấy rule ưu tiên cao nhất. Giá đã gồm thuế và làm tròn đến 1 VND. Member segment có trong Release 2; coupon, promotion code và gate surcharge upgrade được deferred. Chi tiết truy vết tại `OQ-009` và `OQ-014`.
