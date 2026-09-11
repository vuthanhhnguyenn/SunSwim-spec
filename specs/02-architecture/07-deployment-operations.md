# Triển khai và vận hành

## 1. Environments

| Environment | Mục đích | Dữ liệu |
|---|---|---|
| Local | Development | Synthetic |
| Dev | Integration liên tục | Synthetic |
| Staging/UAT | E2E với provider sandbox/device test | Masked/synthetic |
| Production | Vận hành thật | Production classified |

Các environment không dùng chung database hoặc secret. Migration chạy một lần, có lock và giữ backward compatibility theo chiến lược expand, migrate, contract.

## 2. Production topology baseline

- Khi HA được duyệt, load balancer và WAF đứng trước ít nhất 2 API instance stateless.
- Worker chạy trên instance riêng và mỗi job có lease.
- Managed PostgreSQL có automated backup và PITR theo RPO.
- Redis managed là thành phần tùy chọn. Mất Redis không được làm sai payment, pass hoặc capacity.
- Attachment, export và receipt được lưu trong private object storage.
- Log, metric, trace và alert được thu thập tập trung.
- Máy quầy lễ tân chạy ứng dụng web và thành phần Local Cache được bảo vệ. Thành phần này lưu dữ liệu tối thiểu cùng hàng đợi lịch sử quét để đồng bộ lại sau sự cố mạng.

Spec này không giả định một cloud hoặc vendor cụ thể.

## 3. CI/CD gates

1. Lint/type/unit tests.
2. API/event schema compatibility check.
3. Migration test trên snapshot schema.
4. Integration tests với PostgreSQL thật.
5. Security/dependency/container/IaC scans.
6. Concurrency tests critical invariants.
7. Deploy staging, smoke test, approval production.
8. Post-deploy gate/payment synthetic checks.

## 4. Release strategy

- Thay đổi API trong `/v1` phải tương thích ngược. Mặc định chỉ thêm field.
- Consumer event deploy trước producer khi thêm field bắt buộc.
- Feature flag cho provider, dynamic pricing và auto locker.
- Database migration không được dựa vào rollback code có thể phá dữ liệu. Với migration đã nhận traffic, đội dự án dùng forward fix.
- Chỉ canary hoặc rolling deploy sau khi đã kiểm thử mixed-version compatibility.

## 5. Backup và disaster recovery

- Automated backup và PITR phải được mã hóa và có access audit.
- Mỗi quý chạy restore drill và ghi lại RPO/RTO thực tế.
- Cấu hình provider, device và infrastructure-as-code phải được version control.
- Recovery order: database → API/device auth → gate access → worker/payment → admin/report.

## 6. Runbooks bắt buộc

| Runbook | Trigger |
|---|---|
| Gate API degraded | latency/error SLO breach |
| Branch network offline | device heartbeat lost |
| Offline queue synchronization | có kết nối trở lại nhưng bản ghi chưa đồng bộ hoặc phát sinh xung đột |
| Capacity drift | counter ≠ open session count |
| Payment webhook backlog | age/depth alert |
| Fulfillment stuck | paid order item pending quá SLA |
| Outbox/DLQ recovery | retry threshold exceeded |
| Open session reconciliation | session age threshold |
| Provider key rotation | scheduled/compromise |
| Database restore | corruption/outage/disaster |
| PII/security incident | detection/report |

## 7. Operational dashboards

- Gate health theo branch/device, last heartbeat, deny/error ratio.
- Thời điểm Local Cache cập nhật gần nhất, số bản ghi ngoại tuyến chờ đồng bộ và số xung đột cần xử lý.
- Capacity current, drift, manual adjustment.
- Payment webhook rate, verify failures, pending age, settlement mismatch.
- Fulfillment backlog và retry count.
- Outbox age, projection lag, notification failures.
- DB saturation, slow query, locks/deadlocks, pool usage.

## Thuật ngữ cần biết

| Thuật ngữ | Giải thích dễ hiểu |
|---|---|
| Environment | Môi trường chạy hệ thống, như Dev, UAT hoặc Production. |
| HA | Thiết kế có nhiều thành phần dự phòng để dịch vụ tiếp tục chạy khi một phần gặp lỗi. |
| PITR | Khôi phục database về một thời điểm cụ thể trong quá khứ. |
| CI/CD | Quy trình tự động kiểm tra, đóng gói và triển khai phần mềm. |
| Runbook | Hướng dẫn từng bước để xử lý một sự cố hoặc công việc vận hành. |
