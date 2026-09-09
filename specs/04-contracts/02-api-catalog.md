# API catalog

Catalog là logical contract để frontend/backend/device thống nhất phạm vi. Request/response schema chi tiết sẽ được baseline trong OpenAPI sau khi các OQ P0 liên quan đóng.

## Customer & entitlement

| Method/path | Operation | Permission | Idempotency/concurrency |
|---|---|---|---|
| `GET /members` | Search/list members | `member.read` + scope | cursor |
| `POST /members` | Create member | `member.create` | key required |
| `GET /members/{id}` | Member detail | scoped/self | – |
| `PATCH /members/{id}` | Update allowed fields | scoped/self | `If-Match` |
| `GET /members/{id}/passes` | Pass list/history | scoped/self | cursor |
| `GET /member-passes/{id}` | Pass detail/ledger summary | scoped/self | – |
| `GET /member-passes/{id}/eligibility` | Explain eligibility | scoped/self/device-internal | no side effect |
| `POST /member-passes/{id}/adjustments` | Entry/expiry adjustment | `pass.adjust` | key + expected version |
| `POST /member-passes/{id}/cancellations` | Cancel pass | `pass.cancel` | key + expected version |
| `GET/POST /pass-products` | List/create draft | read/configure | key on POST |
| `PATCH /pass-products/{id}` | Edit draft/new version | `product.configure` | `If-Match` |
| `POST /pass-products/{id}/publication` | Publish version | `product.publish` | key + expected version |

## Access & facility

| Method/path | Operation | Permission | Notes |
|---|---|---|---|
| `POST /gate/access-requests` | IN/OUT decision | device auth | key/requestId required |
| `POST /gate/access-events/{id}/acknowledgements` | Physical gate result | same device/gate | idempotent |
| `GET /branches/{id}/capacity` | Current snapshot | `capacity.read` | includes `asOf` |
| `GET /branches/{id}/access-events` | Event list | `access.read` | cursor + scoped |
| `POST /access-events/{id}/overrides` | Linked override | `access.override` | key + reason |
| `POST /access-sessions/{id}/reconciliations` | Close/correct unresolved | `access.reconcile` | key + reason |
| `GET /realtime/capacity` | SSE stream | `capacity.read` | snapshot on reconnect |
| `GET/POST /lockers` | List/create locker | read/configure | scope |
| `PATCH /lockers/{id}` | Edit config | `locker.configure` | `If-Match` |
| `POST /locker-assignments` | Assign | `locker.assign`/internal | key |
| `POST /locker-assignments/{id}/releases` | Release | `locker.release`/internal | key |
| `POST /lockers/{id}/operational-status-changes` | Maintenance/emergency | permission-specific | key + reason |
| `POST /lockers/{id}/emergency-unlocks` | Unlock command | `locker.emergency_unlock` | step-up + key |
| `POST /locker-device-events` | Device callback | gateway auth | provider event dedupe |

## Freeze

| Method/path | Operation | Permission | Notes |
|---|---|---|---|
| `GET /freeze-requests` | Work queue/history | scoped/own | cursor |
| `POST /freeze-requests` | Submit request | scoped/own | key |
| `GET /freeze-requests/{id}` | Detail | scoped/own | – |
| `POST /freeze-requests/{id}/decisions` | Approve/reject | `freeze.approve` | key + expected version |
| `POST /freeze-requests/{id}/cancellations` | Cancel | scoped/own | key + expected version |
| `POST /freeze-periods/{id}/early-resumptions` | Resume early | permission/policy | key + reason |
| `GET /member-passes/{id}/freeze-entitlement` | Preview quota | scoped/own | no side effect |

## Training

| Method/path | Operation | Permission | Notes |
|---|---|---|---|
| `GET/POST /class-definitions` | List/create template | read/configure | key on POST |
| `GET/POST /class-offerings` | List/create offering | read/configure | key on POST |
| `GET/PATCH /class-offerings/{id}` | Detail/update | scoped | `If-Match` on PATCH |
| `POST /class-offerings/{id}/publication` | Publish/open | `class.publish` | key + expected version |
| `GET /class-sessions` | Calendar/search | scoped/own | cursor/time filter |
| `POST /class-offerings/{id}/enrollments` | Enroll/hold seat | scoped/own | key |
| `POST /enrollments/{id}/cancellations` | Cancel | scoped/own | key + policy result |
| `POST /class-offerings/{id}/waitlist-entries` | Join waitlist | scoped/own | key |
| `GET /coaches/{id}/schedule` | Coach schedule | assigned/scoped | time filter |
| `PUT /class-sessions/{id}/attendance` | Replace roster outcomes | assigned/manager | `If-Match` + key |

## Commerce & pricing

| Method/path | Operation | Permission | Notes |
|---|---|---|---|
| `POST /price-quotes` | Resolve sell price | scoped/own | short-lived quote |
| `POST /orders` | Create order from quote | POS/online | key required |
| `GET /orders` | Search orders | scoped/own | cursor |
| `GET /orders/{id}` | Order/payment/fulfillment | scoped/own | – |
| `POST /orders/{id}/payment-attempts` | Initiate online/QR | scoped/own | key |
| `POST /orders/{id}/cash-payments` | Settle cash | `payment.cash` | key + shift/branch |
| `POST /payments/webhooks/{provider}` | Provider callback | signature | provider dedupe |
| `POST /payments/{id}/refund-requests` | Request refund | scoped/own | key |
| `POST /refund-requests/{id}/decisions` | Approve/reject | `refund.approve` | key + separation rule |
| `GET /orders/{id}/receipt` | Receipt | scoped/own | immutable/versioned |
| `POST /fulfillments/{id}/retries` | Manual retry | `fulfillment.retry` | key |
| `GET/POST /swimming-slots` | Slot list/create | read/configure | key on POST |
| `GET/POST /pricing-rules` | Rule list/create | read/configure | key on POST |
| `POST /pricing-rules/{id}/publication` | Publish | `pricing.publish` | key + expected version |
| `GET/POST /holiday-calendars` | Calendar config | read/configure | versioned |
| `GET/POST /entitlement-time-rules` | Access time rules | read/configure | versioned |
| `POST /pricing-simulations` | Admin preview matrix | `pricing.read` | no business side effect |

## Reporting

| Method/path | Operation | Permission | Notes |
|---|---|---|---|
| `GET /reports/revenue` | Revenue aggregate | `report.revenue.read` | basis/timezone/freshness |
| `GET /reports/traffic` | Check-in/visitor buckets | `report.operations.read` | explicit bucket |
| `GET /reports/capacity` | Occupancy analytics | `report.operations.read` | snapshot/timeline |
| `GET /reports/classes` | Class utilization | `report.class.read` | scoped |
| `POST /report-exports` | Async export job | report-specific export | key |
| `GET /report-exports/{id}` | Job status | creator/scoped | – |
| `GET /report-exports/{id}/download` | Authorized download | creator/scoped | short-lived response |

## Internal-only application contracts

- `Commerce.fulfill(orderItemId)` → Entitlement/Training/Facility.
- `Access.decide(request)` → Entitlement eligibility + Facility reservation.
- `Reporting.project(event)` → projection tables.
- `Platform.publishOutbox()` và `Platform.deliverNotification()`.

Các contract nội bộ không public qua HTTP trừ khi sau này tách service; vẫn phải idempotent và versioned.

