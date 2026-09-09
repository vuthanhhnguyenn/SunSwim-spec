# Kế hoạch MVP và các release

## 1. Nguyên tắc phân rã

- Mỗi release phải tạo ra một vertical slice có thể vận hành và đối soát, không chỉ hoàn thành riêng UI hoặc database.
- Platform control, audit, idempotency và observability phải được triển khai cùng feature đầu tiên thay vì để đến cuối.
- Online payment chỉ được go-live sau khi đã kiểm thử webhook, reconciliation, refund và fulfillment recovery.
- Gate chỉ được pilot sau khi đội dự án kiểm thử race condition, device authentication và diễn tập branch fallback.

## 2. Release 0: Foundation

### Scope

- Organization/branch/zone/gate master data.
- Identity, RBAC + branch scope, audit log.
- PostgreSQL migration baseline, idempotency, outbox/inbox.
- API conventions/problem details, observability, CI/CD, environments.
- Member basic profile và catalog skeleton.

### Điều kiện hoàn thành

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

### Trình tự pilot

1. Shadow mode: gate API ra quyết định nhưng chưa điều khiển gate.
2. Staff-only pilot ngoài peak.
3. Một gate/một branch với manual fallback.
4. Mở rộng toàn branch sau error/latency/capacity reconciliation đạt gate.
5. Mở branch tiếp theo sau post-pilot review.

### Điều kiện hoàn thành

- Tất cả acceptance criteria P0 của R1 phải đạt, gồm cả concurrent last-entry và last-capacity.
- Gate load SLO, fail-closed và fallback runbook pass.
- Daily payment/order/fulfillment và capacity reconciliation không có unexplained variance.
- Các OQ từ `003` đến `005`, `013` và `019` liên quan đến R1 phải được đóng.

## 4. Release 2: Operational Expansion

### Scope

- Freeze lifecycle.
- Locker temporary/fixed + optional IoT adapter.
- Online/QR payment provider, split payment nếu duyệt.
- Refund workflow.
- Opening hours, slot, dynamic pricing/holiday.
- Async exports và advanced reports.

### Điều kiện hoàn thành

- Provider certification/sandbox E2E và webhook replay/late settlement tests.
- Refund/entitlement compensation và finance reconciliation pass.
- Freeze boundary/scheduler correctness pass.
- Locker failure/clearance runbook pass.
- Các OQ từ `007` đến `010`, `012`, `014`, `017` và `021` phải được đóng theo scope.

## 5. Release 3: Training Services

### Scope

- Class Definition + Offering + Session.
- Coach schedule/conflict.
- Enrollment/payment/seat reservation.
- Waitlist, cancellation, attendance.
- Member self-service training views và class reporting.

### Điều kiện hoàn thành

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

Một candidate ở trạng thái Deferred không bị loại vĩnh viễn. Muốn đưa candidate vào release, nhóm phải có business case và impact analysis.

## Thuật ngữ cần biết

| Thuật ngữ | Giải thích dễ hiểu |
|---|---|
| MVP | Phiên bản nhỏ nhất có đủ giá trị để thử nghiệm trong thực tế. |
| Release | Một đợt phát hành gồm một nhóm chức năng có thể sử dụng được. |
| Vertical slice | Phần chức năng hoàn chỉnh từ giao diện đến dữ liệu và xử lý phía sau. |
| Pilot | Triển khai thử trong phạm vi nhỏ trước khi mở rộng. |
| Exit gate | Bộ điều kiện phải đạt trước khi kết thúc một release hoặc chuyển giai đoạn. |
