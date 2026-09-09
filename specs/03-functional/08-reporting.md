# Đặc tả chức năng 08: Báo cáo, Dashboard và Export

## 1. Mục tiêu

Module cung cấp số liệu vận hành và tài chính với cách tính, freshness cùng khả năng drill-down và reconciliation rõ ràng. Reporting chỉ đọc dữ liệu hoặc tạo projection, không sửa transaction nguồn.

## 2. Actors và scope

| Report | Manager | Reception | Coach | Finance |
|---|---:|---:|---:|---:|
| Revenue | Branch/org | Limited branch | No | Branch/org |
| Sales detail | Branch/org | Limited branch | No | Branch/org |
| Traffic/capacity | Branch/org | Branch | No | Read optional |
| Class/attendance | Branch/org | Branch | Assigned only | Read optional |
| Export | Permission | Conditional | Assigned class only | Permission |

Backend phải áp dụng branch scope và field scope trước khi aggregation hoặc tính total count.

## 3. Time and accounting basis

- Filter `from` inclusive, `to` exclusive.
- Business day dùng timezone của branch. Báo cáo nhiều branch phải ghi rõ dữ liệu được nhóm theo ngày địa phương của từng branch hay theo organization reporting timezone.
- Revenue operational default cash basis tại `settled_at`.
- Refund có hai view: refund activity theo `refunded_at`, hoặc restated sale cohort theo ngày của order gốc nếu Finance yêu cầu.
- UI luôn hiển thị timezone, basis và `dataFreshness`.

## 4. Revenue dashboard

KPI gồm gross settled, refund, net revenue, paid order và average paid order. Các dimension là branch, ngày, tuần, tháng, channel, product category và payment method.

Khi drill-down từ aggregate xuống order, payment hoặc refund, backend phải kiểm tra permission lại. Các trạng thái `PENDING/FAILED/CANCELLED` không được tính vào settled revenue.

## 5. Operations dashboard

- Current occupancy/limit/threshold/asOf.
- Today's check-ins và unique visitors tách biệt.
- Peak occupancy, arrivals/departures by bucket.
- Gate/device health và unresolved sessions.
- Locker availability/utilization nếu module enabled.

Current occupancy được đọc từ capacity state hoặc snapshot, không tính trực tiếp bằng số lượt check-in.

## 6. Class dashboard

- Offering/session fill rate, waitlist, cancellation.
- Attendance outcomes, no-show rate.
- Coach/session utilization nếu scope được duyệt.
- Không mặc định đưa payroll/compensation.

## 7. Projection behavior

- Event ID và inbox ID được dùng để tránh double count.
- Projection row có source version/checkpoint.
- Late/out-of-order events được upsert theo aggregate version hoặc recompute affected bucket.
- Rebuild chạy side-by-side hoặc theo bounded window để dashboard vẫn dùng được nguồn dữ liệu hiện tại.
- Projection lag được đo và hiển thị.

## 8. Export

1. User tạo export job với filter/schema version.
2. Server kiểm tra quyền, lưu snapshot của scope và estimate kích thước.
3. Worker tạo CSV hoặc XLSX tùy khả năng hỗ trợ và masking field theo permission.
4. File private, checksum, expires_at, download audit.
5. Job state `QUEUED → RUNNING → SUCCEEDED|FAILED|EXPIRED`.
6. Retry idempotent không được làm file trở thành công khai hoặc gửi notification trùng ngoài ý muốn.

## 9. API impacts

- `GET /api/v1/reports/revenue`
- `GET /api/v1/reports/traffic`
- `GET /api/v1/reports/capacity`
- `GET /api/v1/reports/classes`
- `POST /api/v1/report-exports`
- `GET /api/v1/report-exports/{jobId}`
- `GET /api/v1/report-exports/{jobId}/download` hoặc short-lived redirect sau authorization

Common response metadata:

```json
{
  "data": [],
  "meta": {
    "timezone": "Asia/Ho_Chi_Minh",
    "basis": "CASH_SETTLEMENT",
    "from": "2026-09-01T00:00:00+07:00",
    "to": "2026-10-01T00:00:00+07:00",
    "dataFreshness": "2026-10-01T00:03:00+07:00"
  }
}
```

## 10. Errors

`REPORT_RANGE_TOO_LARGE`, `REPORT_FILTER_INVALID`, `REPORT_NOT_READY`, `EXPORT_LIMIT_EXCEEDED`, `EXPORT_EXPIRED`, `DATA_SCOPE_FORBIDDEN`, `PROJECTION_STALE`.

## 11. Acceptance criteria

- `AC-RPT-001`: Duplicate payment/access event không double count projection.
- `AC-RPT-002`: Pending/failed payment không vào revenue; settled refund giảm net.
- `AC-RPT-003`: Hai check-ins cùng member trong ngày cho check-ins 2, unique visitors 1.
- `AC-RPT-004`: Occupancy timeline phản ánh open sessions/checkout, không chỉ arrivals.
- `AC-RPT-005`: Reception branch A không query/export branch B bằng cách sửa request.
- `AC-RPT-006`: UI/report response luôn có timezone/basis/freshness.
- `AC-RPT-007`: Export link hết hạn/khác user không tải được.
- `AC-RPT-008`: Rebuild projection cho cùng source tạo cùng result.

## 12. Quyết định baseline

Reporting timezone của tổ chức là `Asia/Ho_Chi_Minh`. Finance cutoff lúc 23:59:59 theo ngày lịch và reconciliation batch chạy từ 00:30 ngày kế tiếp. Báo cáo trực tiếp tối đa 31 ngày; export CSV hoặc XLSX tối đa 100.000 dòng; link tải hết hạn sau 24 giờ. PII được masking khi người dùng không có quyền chi tiết. Chi tiết truy vết tại `OQ-012`, `OQ-013` và `OQ-021`.

## Thuật ngữ cần biết

| Thuật ngữ | Giải thích dễ hiểu |
|---|---|
| Aggregate | Số liệu được tổng hợp từ nhiều giao dịch chi tiết. |
| Projection | Bảng dữ liệu được tạo riêng để báo cáo đọc nhanh hơn. |
| Drill-down | Mở từ số liệu tổng xuống các giao dịch chi tiết tạo ra số liệu đó. |
| Masking | Che một phần dữ liệu nhạy cảm khi người dùng không có đủ quyền. |
| Cutoff | Mốc thời gian chốt dữ liệu cho một kỳ báo cáo hoặc đối soát. |
