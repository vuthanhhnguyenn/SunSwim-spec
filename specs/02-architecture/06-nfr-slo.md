# Non-functional requirements và SLO

Các số liệu dưới đây là baseline để lập kế hoạch. Engineering sẽ đo lại bằng performance test. Nếu cần đổi ngưỡng, nhóm phải thực hiện Change Control Process.

## 1. Performance

| ID | SLI | Target |
|---|---|---|
| NFR-PERF-001 | Gate API server latency, không tính physical actuation | P95 ≤ 800 ms, P99 ≤ 1.5 s |
| NFR-PERF-002 | Admin transactional API | P95 ≤ 1.5 s |
| NFR-PERF-003 | Pricing quote | P95 ≤ 500 ms |
| NFR-PERF-004 | Live capacity event freshness | P95 ≤ 5 s |
| NFR-PERF-005 | Standard report ≤ 31 ngày/1 branch | P95 ≤ 5 s |
| NFR-PERF-006 | Dataset lớn | Async export; request tạo job ≤ 2 s |

Độ trễ tại server và end-to-end phải được đo riêng. Khi dữ liệu stale hoặc mất kết nối, dashboard phải hiển thị đúng trạng thái thay vì coi dữ liệu cũ là realtime.

## 2. Availability và resilience

| ID | Target |
|---|---|
| NFR-AVL-001 | Gate/API monthly availability 99.9%, loại trừ maintenance đã thông báo nếu hợp đồng cho phép |
| NFR-AVL-002 | Admin/Member API monthly availability 99.5% |
| NFR-AVL-003 | Provider timeout dùng retry có backoff chỉ với operation an toàn/idempotent |
| NFR-AVL-004 | Notification/report failure không làm lỗi access/payment commit |
| NFR-AVL-005 | Worker backlog age có alert trước khi vi phạm freshness SLO |

## 3. Data integrity

- `NFR-DATA-001`: Không negative pass balance/capacity.
- `NFR-DATA-002`: Không duplicate payment application/fulfillment với cùng idempotency identity.
- `NFR-DATA-003`: Mọi state-changing request có request/correlation ID.
- `NFR-DATA-004`: Point-in-time restore được bật nếu nền tảng hỗ trợ; restore test định kỳ.
- `NFR-DATA-005`: RPO không quá 15 phút và RTO không quá 4 giờ.

## 4. Security

- `NFR-SEC-001`: TLS mọi external/internal network path chứa credential/PII.
- `NFR-SEC-002`: Privileged staff MFA và least privilege.
- `NFR-SEC-003`: Secret trong managed secret store, rotation định kỳ và on-demand.
- `NFR-SEC-004`: Critical audit coverage 100% theo catalog.
- `NFR-SEC-005`: Critical/High vulnerability không được release nếu chưa có risk acceptance có hạn.

## 5. Scalability assumptions để test

Estimate và kiểm thử dùng tải thiết kế sau:

- 3 branches, 10 gates, 300 staff, 100,000 members.
- Burst 20 gate requests/second toàn hệ thống.
- 2 million access events/year.
- 200 concurrent admin/member sessions.

Load test phải bao gồm trường hợp nhiều request dùng cùng QR hoặc pass, capacity chỉ còn một chỗ, webhook trùng theo burst và report chạy cùng lúc với gate traffic.

## 6. Observability

- Structured log gồm `timestamp`, `level`, `service`, `module`, `requestId`, `correlationId`, `branchId`, `actorType`, `resultCode` và duration. PII phải được masking.
- Metrics: request rate/error/latency, DB pool/locks, outbox backlog, webhook verify failures, gate deny reasons, capacity drift, open-session age, projection lag.
- Traces cho gate, payment settlement và fulfillment; không đưa token/PII vào span.
- Mỗi alert phải có runbook, severity và owner.

## 7. Accessibility và compatibility

- Admin/member web hướng tới WCAG 2.2 AA cho luồng cốt lõi.
- Responsive từ mobile member đến desktop POS.
- Browser support matrix được chốt trước UAT; kiosk/device browser version thuộc contract triển khai.

## Thuật ngữ cần biết

| Thuật ngữ | Giải thích dễ hiểu |
|---|---|
| NFR | Yêu cầu phi chức năng, chẳng hạn tốc độ, bảo mật hoặc khả năng phục hồi. |
| SLI | Chỉ số thực tế dùng để đo chất lượng dịch vụ. |
| SLO | Mục tiêu cụ thể mà một SLI phải đạt. |
| P95/P99 | Mốc mà 95% hoặc 99% request có thời gian xử lý không vượt quá giá trị đó. |
| RPO/RTO | Mức dữ liệu có thể mất và thời gian tối đa để khôi phục dịch vụ sau sự cố. |
