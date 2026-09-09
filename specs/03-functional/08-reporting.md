# Functional Spec 08: Reporting, Dashboard và Export

## 1. Mục tiêu

Cung cấp số liệu vận hành/tài chính có định nghĩa, freshness và khả năng drill-down/reconcile rõ. Reporting chỉ đọc/projection, không sửa transaction nguồn.

## 2. Actors và scope

| Report | Manager | Reception | Coach | Finance |
|---|---:|---:|---:|---:|
| Revenue | Branch/org | Limited branch | No | Branch/org |
| Sales detail | Branch/org | Limited branch | No | Branch/org |
| Traffic/capacity | Branch/org | Branch | No | Read optional |
| Class/attendance | Branch/org | Branch | Assigned only | Read optional |
| Export | Permission | Conditional | Assigned class only | Permission |

Backend apply branch/field scope trước aggregation và total count.

## 3. Time and accounting basis

- Filter `from` inclusive, `to` exclusive.
- Business day theo branch timezone; multi-branch report group từng branch local day hoặc organization reporting timezone được chọn rõ.
- Revenue operational default cash basis tại `settled_at`.
- Refund có hai views: refund activity by `refunded_at`; restated sale cohort by original order date nếu Finance yêu cầu.
- UI luôn hiển thị timezone, basis và `dataFreshness`.

## 4. Revenue dashboard

KPIs: gross settled, refunds, net revenue, paid orders, average paid order. Dimensions: branch, day/week/month, channel, product category, payment method.

Drill-down từ aggregate đến order/payment/refund được permission check lại. `PENDING/FAILED/CANCELLED` không vào settled revenue.

## 5. Operations dashboard

- Current occupancy/limit/threshold/asOf.
- Today's check-ins và unique visitors tách biệt.
- Peak occupancy, arrivals/departures by bucket.
- Gate/device health và unresolved sessions.
- Locker availability/utilization nếu module enabled.

Current occupancy đọc capacity state/snapshot, không tính bằng check-in count đơn giản.

## 6. Class dashboard

- Offering/session fill rate, waitlist, cancellation.
- Attendance outcomes, no-show rate.
- Coach/session utilization nếu scope được duyệt.
- Không mặc định đưa payroll/compensation.

## 7. Projection behavior

- Event/inbox ID tránh double count.
- Projection row có source version/checkpoint.
- Late/out-of-order events được upsert theo aggregate version hoặc recompute affected bucket.
- Rebuild chạy side-by-side hoặc theo bounded window, không làm dashboard mất nguồn dữ liệu hiện tại.
- Projection lag được đo và hiển thị.

## 8. Export

1. User tạo export job với filter/schema version.
2. Server authorize, snapshot scope và estimate size.
3. Worker generate CSV/XLSX tùy support; mask field theo permission.
4. File private, checksum, expires_at, download audit.
5. Job state `QUEUED → RUNNING → SUCCEEDED|FAILED|EXPIRED`.
6. Retry idempotent không tạo file công khai/trùng notification ngoài ý muốn.

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
