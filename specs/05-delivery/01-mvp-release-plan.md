# MVP và release plan

## 1. Nguyên tắc phân rã

- Mỗi release là vertical slice có thể vận hành/đối soát, không chỉ hoàn thành UI hoặc database.
- Platform controls, audit, idempotency và observability đi cùng feature đầu tiên, không để cuối.
- Online payment chỉ go-live khi webhook, reconciliation, refund và fulfillment recovery đã test.
- Gate chỉ pilot khi race conditions, device auth và branch fallback đã diễn tập.

## 2. Release 0: Foundation

### Scope

- Organization/branch/zone/gate master data.
- Identity, RBAC + branch scope, audit log.
- PostgreSQL migration baseline, idempotency, outbox/inbox.
- API conventions/problem details, observability, CI/CD, environments.
- Member basic profile và catalog skeleton.

### Exit gate

- Security/threat model approved.
- Authorization/branch isolation tests pass.
- Backup restore smoke test.
- OQ P0 ảnh hưởng data model đã đóng: `OQ-001`, `OQ-002`, `OQ-006`, `OQ-011`.

## 3. Release 1: Core Pool Operation

### Scope

- Member + 3 pass types, product/version, issue via order.
- POS cash, order/receipt, basic fulfillment.
- QR credential, online gate check-in/out, presence, capacity.
- Manual override/reconciliation.
- Basic revenue/traffic/capacity dashboard.
- In-app notifications tối thiểu.

### Pilot sequence

1. Shadow mode: gate API ra quyết định nhưng chưa điều khiển gate.
2. Staff-only pilot ngoài peak.
3. Một gate/một branch với manual fallback.
4. Mở rộng toàn branch sau error/latency/capacity reconciliation đạt gate.
5. Mở branch tiếp theo sau post-pilot review.

### Exit gate

- All P0 R1 AC pass, gồm concurrent last-entry/last-capacity.
- Gate load SLO, fail-closed và fallback runbook pass.
- Daily payment/order/fulfillment và capacity reconciliation không có unexplained variance.
- OQ `003–005`, `013`, `019` liên quan R1 đóng.

## 4. Release 2: Operational Expansion

### Scope

- Freeze lifecycle.
- Locker temporary/fixed + optional IoT adapter.
- Online/QR payment provider, split payment nếu duyệt.
- Refund workflow.
- Opening hours, slot, dynamic pricing/holiday.
- Async exports và advanced reports.

### Exit gate

- Provider certification/sandbox E2E và webhook replay/late settlement tests.
- Refund/entitlement compensation và finance reconciliation pass.
- Freeze boundary/scheduler correctness pass.
- Locker failure/clearance runbook pass.
- OQ `007–010`, `012`, `014`, `017`, `021` đóng theo scope.

## 5. Release 3: Training Services

### Scope

- Class Definition + Offering + Session.
- Coach schedule/conflict.
- Enrollment/payment/seat reservation.
- Waitlist, cancellation, attendance.
- Member self-service training views và class reporting.

### Exit gate

- Course/drop-in model và make-up policy approved.
- Coach/student overlap/capacity race tests pass.
- Minor/guardian/waiver decision closed nếu phục vụ trẻ em.
- Cancellation/refund/notification E2E pass.

## 6. Deferred candidate backlog

- Household/guardian portal và digital waivers.
- Recurring billing/auto-renewal/dunning.
- Swimmer level/progression and make-up credits.
- Lane/facility booking.
- Gate offline credential cache.
- Native mobile/push, advanced CRM/marketing.
- Accounting/e-invoice connector, payroll/coach compensation.

Deferred không đồng nghĩa bị loại vĩnh viễn; mỗi candidate cần business case và impact analysis.

