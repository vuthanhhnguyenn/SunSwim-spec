# Non-functional requirements và SLO

Các số liệu dưới đây có trạng thái `BASELINED` cho lập kế hoạch. Engineering đo lại trong performance test; thay đổi ngưỡng phải đi qua Change Control Process.

## 1. Performance

| ID | SLI | Target |
|---|---|---|
| NFR-PERF-001 | Gate API server latency, không tính physical actuation | P95 ≤ 800 ms, P99 ≤ 1.5 s |
| NFR-PERF-002 | Admin transactional API | P95 ≤ 1.5 s |
| NFR-PERF-003 | Pricing quote | P95 ≤ 500 ms |
| NFR-PERF-004 | Live capacity event freshness | P95 ≤ 5 s |
| NFR-PERF-005 | Standard report ≤ 31 ngày/1 branch | P95 ≤ 5 s |
| NFR-PERF-006 | Dataset lớn | Async export; request tạo job ≤ 2 s |

Đo tại server và end-to-end riêng; dashboard phải hiển thị stale/disconnected state thay vì dữ liệu cũ như realtime.

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

Đây là tải thiết kế dùng cho estimate và kiểm thử:

- 3 branches, 10 gates, 300 staff, 100,000 members.
- Burst 20 gate requests/second toàn hệ thống.
- 2 million access events/year.
- 200 concurrent admin/member sessions.

Load test phải gồm cùng QR/pass, capacity còn một chỗ, webhook duplicate burst và report chạy đồng thời gate traffic.

## 6. Observability

- Structured logs có `timestamp`, `level`, `service`, `module`, `requestId`, `correlationId`, `branchId`, `actorType`, `resultCode`, duration; PII được mask.
- Metrics: request rate/error/latency, DB pool/locks, outbox backlog, webhook verify failures, gate deny reasons, capacity drift, open-session age, projection lag.
- Traces cho gate, payment settlement và fulfillment; không đưa token/PII vào span.
- Alert phải gắn runbook, severity và owner.

## 7. Accessibility và compatibility

- Admin/member web hướng tới WCAG 2.2 AA cho luồng cốt lõi.
- Responsive từ mobile member đến desktop POS.
- Browser support matrix được chốt trước UAT; kiosk/device browser version thuộc contract triển khai.
