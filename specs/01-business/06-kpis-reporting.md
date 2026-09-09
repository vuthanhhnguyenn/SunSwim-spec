# KPI và cách tính báo cáo

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

## 2. Các chiều phân tích

- Business time dùng timezone của branch, với `from` inclusive và `to` exclusive.
- Các chiều phân tích gồm branch, channel, product category, payment method và pass type.
- Drill-down chỉ hiển thị Member hoặc PII khi người dùng có permission. Dữ liệu aggregate không cần để lộ PII.
- Refund attribution phải hỗ trợ cả refund date và original sale date. Giao diện cần ghi rõ basis đang dùng.

## 3. Freshness

| Report | Target freshness | Hiển thị |
|---|---|---|
| Live capacity | ≤ 5 giây ở điều kiện bình thường | `asOf` và connection state |
| Traffic dashboard | ≤ 1 phút | `dataFreshness` |
| Revenue operational | ≤ 5 phút sau settlement | `lastProjectedAt` |
| Finance export | Sau reconciliation run | `reconciledThrough` |

## 4. Reconciliation

Daily job thực hiện năm phép đối chiếu:

1. Provider settlement với payment ledger.
2. Paid order total với tổng successful payment allocation.
3. Fulfilled items với entitlement/enrollment/rental thực tế.
4. Session mở bất thường với gate/access event.
5. Projection totals với nguồn transaction.

Mỗi chênh lệch phải tạo exception record và đi qua workflow xử lý. Job không được tự sửa transaction lịch sử.

## Thuật ngữ cần biết

| Thuật ngữ | Giải thích dễ hiểu |
|---|---|
| KPI | Chỉ số dùng để theo dõi kết quả vận hành hoặc kinh doanh. |
| Metric | Một đại lượng được định nghĩa rõ và có cách đo cụ thể. |
| Grain | Mức chi tiết nhỏ nhất của dữ liệu, chẳng hạn theo payment hoặc theo ngày. |
| Drill-down | Đi từ số liệu tổng hợp xuống các giao dịch tạo nên số liệu đó. |
| Freshness | Mức độ mới của dữ liệu so với thời điểm hiện tại. |
