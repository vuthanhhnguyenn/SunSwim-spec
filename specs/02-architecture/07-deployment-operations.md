# Deployment và operations

## 1. Environments

| Environment | Mục đích | Dữ liệu |
|---|---|---|
| Local | Development | Synthetic |
| Dev | Integration liên tục | Synthetic |
| Staging/UAT | E2E với provider sandbox/device test | Masked/synthetic |
| Production | Vận hành thật | Production classified |

Không chia sẻ database/secret giữa environment. Migration chạy một lần có lock và backward-compatible theo chiến lược expand/migrate/contract.

## 2. Production topology baseline

- Load balancer/WAF → ≥2 API instances stateless khi HA được duyệt.
- Worker instances riêng, job có lease.
- Managed PostgreSQL có automated backup/PITR theo RPO.
- Redis managed optional, mất Redis không làm sai payment/pass/capacity.
- Private object storage cho attachment/export/receipt.
- Centralized logs, metrics, traces và alerting.

Cloud/vendor cụ thể không được giả định trong spec này.

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

- API change tương thích ngược trong `/v1`; additive field là default.
- Consumer event deploy trước producer khi thêm field bắt buộc.
- Feature flag cho provider, dynamic pricing và auto locker.
- Database migration không phụ thuộc rollback code phá dữ liệu; dùng forward fix cho migration đã có traffic.
- Canary/rolling deploy chỉ khi mixed-version compatibility đã test.

## 5. Backup và disaster recovery

- Automated backup/PITR; mã hóa và access audit.
- Quarterly restore drill baseline, ghi actual RPO/RTO.
- Export cấu hình provider/device và infrastructure-as-code được version control.
- Recovery order: database → API/device auth → gate access → worker/payment → admin/report.

## 6. Runbooks bắt buộc

| Runbook | Trigger |
|---|---|
| Gate API degraded | latency/error SLO breach |
| Branch network offline | device heartbeat lost |
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
- Capacity current, drift, manual adjustment.
- Payment webhook rate, verify failures, pending age, settlement mismatch.
- Fulfillment backlog và retry count.
- Outbox age, projection lag, notification failures.
- DB saturation, slow query, locks/deadlocks, pool usage.

