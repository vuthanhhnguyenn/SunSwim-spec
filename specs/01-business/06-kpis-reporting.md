# KPI và định nghĩa báo cáo

## 1. Metric dictionary

| Metric | Định nghĩa | Source of truth | Grain |
|---|---|---|---|
| Gross settled revenue | Tổng payment `SETTLED`, không gồm refund | Payment ledger | payment/branch/time |
| Settled refunds | Tổng refund `SETTLED` | Refund ledger | refund/branch/time |
| Net revenue | Gross settled revenue − settled refunds | Payment + Refund | ngày/branch/currency |
| Orders paid | Số order đạt `PAID` trong kỳ theo `paid_at` | Order | order |
| Average paid order | Net revenue / số paid order; hiển thị N/A nếu 0 | Projection | kỳ |
| Check-ins | Số access event `ALLOW/CHECK_IN` không phải replay | Access event | event |
| Unique visitors | `count(distinct member_or_guest_subject)` có ALLOW trong ngày branch | Access event | business day |
| Current occupancy | Số session open và counts toward capacity | Access session | branch/zone/current |
| Peak occupancy | Max occupancy snapshot/derived timeline trong bucket | Session/snapshot | bucket |
| Class utilization | Confirmed seats / capacity tại cutoff | Enrollment/session | session |
| No-show rate | NoShow / attendance-eligible enrollment | Attendance | session/course |
| Locker utilization | Occupied locker-minutes / available locker-minutes | Assignment/status history | branch/day |

## 2. Reporting dimensions

- Business time: branch timezone, with explicit `from` inclusive và `to` exclusive.
- Branch, channel, product category, payment method, pass type.
- Member/PII chỉ trong drill-down có permission; aggregate không cần lộ PII.
- Refund attribution phải hỗ trợ cả refund date và original sale date; UI ghi rõ basis.

## 3. Freshness

| Report | Target freshness | Hiển thị |
|---|---|---|
| Live capacity | ≤ 5 giây ở điều kiện bình thường | `asOf` và connection state |
| Traffic dashboard | ≤ 1 phút | `dataFreshness` |
| Revenue operational | ≤ 5 phút sau settlement | `lastProjectedAt` |
| Finance export | Sau reconciliation run | `reconciledThrough` |

## 4. Reconciliation

Daily job so sánh:

1. Provider settlement với payment ledger.
2. Paid order total với tổng successful payment allocation.
3. Fulfilled items với entitlement/enrollment/rental thực tế.
4. Session mở bất thường với gate/access event.
5. Projection totals với nguồn transaction.

Mọi chênh lệch tạo exception record và workflow xử lý; không tự sửa transaction lịch sử.

