# Non-functional requirements và SLO

Các số liệu dưới đây là baseline để lập kế hoạch. Engineering sẽ đo lại bằng performance test. Nếu cần đổi ngưỡng, nhóm phải thực hiện Change Control Process.

## 1. Performance

| ID | Chỉ số | Mục tiêu |
|---|---|---|
| NFR-PERF-001 | Toàn bộ thao tác quét check-in và gán tủ tại quầy | Dưới 5 giây cho mỗi khách |
| NFR-PERF-002 | Thời gian phản hồi API ở tải thiết kế | Không quá 1,5 giây |
| NFR-PERF-003 | Sức chịu tải tại giờ cao điểm | Ít nhất 200 giao dịch đồng thời |
| NFR-PERF-004 | Trùng lịch học bơi do hệ thống chấp nhận | 0 trường hợp |
| NFR-PERF-005 | Thất thoát vé do cấp hoặc ghi nhận sai | 0 trường hợp |

Thời gian API và thời gian thao tác từ lúc quét đến khi hiện kết quả gán tủ phải được đo riêng. Kiểm thử cần chạy với dữ liệu gần thực tế, có giao dịch check-in, bán vé, xếp lịch và báo cáo diễn ra đồng thời.

## 2. Availability và resilience

| ID | Target |
|---|---|
| NFR-AVL-001 | Khi mất Internet, quầy dùng Local Cache để kiểm tra check-in có kiểm soát và lưu lịch sử quét tạm thời |
| NFR-AVL-002 | Khi có mạng trở lại, dữ liệu ngoại tuyến tự đồng bộ và không tạo tác động trùng |
| NFR-AVL-003 | Provider timeout chỉ được retry với thao tác an toàn hoặc có idempotency |
| NFR-AVL-004 | Lỗi notification hoặc báo cáo không được làm hỏng giao dịch access hay payment đã commit |
| NFR-AVL-005 | Hàng đợi đồng bộ và worker phải có cảnh báo khi tồn đọng vượt ngưỡng vận hành được duyệt |

## 3. Data integrity

- `NFR-DATA-001`: Không negative pass balance/capacity.
- `NFR-DATA-002`: Không duplicate payment application/fulfillment với cùng idempotency identity.
- `NFR-DATA-003`: Mọi state-changing request có request/correlation ID.
- `NFR-DATA-004`: Point-in-time restore được bật nếu nền tảng hỗ trợ; restore test định kỳ.
- `NFR-DATA-005`: Bản ghi quét ngoại tuyến phải truy vết được từ máy quầy đến kết quả đối soát trên máy chủ.

## 4. Security

- `NFR-SEC-001`: TLS mọi external/internal network path chứa credential/PII.
- `NFR-SEC-002`: Privileged staff MFA và least privilege.
- `NFR-SEC-003`: Secret trong managed secret store, rotation định kỳ và on-demand.
- `NFR-SEC-004`: Critical audit coverage 100% theo catalog.
- `NFR-SEC-005`: Critical/High vulnerability không được release nếu chưa có risk acceptance có hạn.

## 5. Scalability assumptions để test

Tải thiết kế tối thiểu là 200 giao dịch đồng thời trong giờ cao điểm. Kịch bản tải phải phối hợp các giao dịch bán vé, check-in/out, gán tủ và xếp lịch, thay vì chỉ mở 200 phiên đăng nhập không tạo giao dịch.

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
| Local Cache | Dữ liệu cần thiết được lưu tạm tại quầy để duy trì check-in khi mất mạng. |
