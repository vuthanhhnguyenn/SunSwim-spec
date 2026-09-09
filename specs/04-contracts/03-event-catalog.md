# Event contract và catalog

## 1. Envelope

Event integration theo CloudEvents 1.0.x JSON envelope:

```json
{
  "specversion": "1.0",
  "id": "uuid",
  "source": "/sunswim/commerce",
  "type": "commerce.payment.settled.v1",
  "subject": "payments/uuid",
  "time": "2026-09-10T01:30:00Z",
  "datacontenttype": "application/json",
  "correlationid": "uuid",
  "causationid": "uuid",
  "aggregateversion": 3,
  "data": {}
}
```

Custom extension names dùng lowercase. `id` ổn định qua retry; publish time không thay event business time.

## 2. Delivery semantics

- Transactional outbox, at-least-once delivery.
- Consumer inbox/dedupe bằng `(consumer,eventId)`.
- Không kỳ vọng global ordering; khi cần dùng subject + aggregate version.
- Consumer bỏ qua duplicate; stale version không được overwrite state mới.
- Event không chứa secret/raw QR/payment token/PII không cần thiết.

## 3. Catalog

| Event type | Producer | Data tối thiểu | Consumers |
|---|---|---|---|
| `customer.member.created.v1` | Customer | memberId, homeBranchId | Audit, reporting |
| `customer.member.status-changed.v1` | Customer | memberId, old/new, reason | Entitlement cache, access projection |
| `catalog.product.published.v1` | Catalog | productId, version, effectiveAt | Commerce cache |
| `catalog.pricing-rule.published.v1` | Catalog | ruleId, version, scopes | Quote cache invalidation |
| `commerce.order.paid.v1` | Commerce | orderId, branchId, paidAt, total, currency | Fulfillment, reporting |
| `commerce.payment.settled.v1` | Commerce | paymentId, orderId, provider, amount, currency | Reporting, reconciliation |
| `commerce.refund.settled.v1` | Commerce | refundId, paymentId, orderId, amount | Reporting, compensation |
| `commerce.fulfillment.requested.v1` | Commerce | fulfillmentId, orderItemId, type, subjectId | Target domain worker |
| `commerce.fulfillment.succeeded.v1` | Target/Commerce | fulfillmentId, resourceType/id | Commerce, notification |
| `entitlement.pass.issued.v1` | Entitlement | memberPassId, memberId, productId, state | Notification, reporting |
| `entitlement.pass.activated.v1` | Entitlement | memberPassId, validFrom, endExclusive | Notification, reporting |
| `entitlement.pass.usage-recorded.v1` | Entitlement | usageId, passId, delta, reason | Reporting/reconciliation |
| `entitlement.freeze.approved.v1` | Entitlement | requestId, passId, start/end, days | Notification, reporting |
| `entitlement.pass.suspended.v1` | Entitlement | passId, periodId, effectiveAt | Access projection, notification |
| `entitlement.pass.resumed.v1` | Entitlement | passId, periodId, effectiveAt | Access projection, notification |
| `access.decision.recorded.v1` | Access | eventId, subjectId, branch/zone, direction, decision/reason | Realtime, reporting |
| `access.session.opened.v1` | Access | sessionId, memberId, passId, branch/zone, at | Facility/locker, reporting |
| `access.session.closed.v1` | Access | sessionId, branch/zone, at, reason | Locker, reporting |
| `access.session.reconciled.v1` | Access | sessionId, correction, reason | Reporting/audit |
| `facility.capacity.changed.v1` | Facility/Access | branch/zone, current, limit, threshold, asOf | Realtime/alerts |
| `facility.capacity.threshold-reached.v1` | Facility | branch/zone, old/new threshold | Notification/ops |
| `facility.locker.assigned.v1` | Facility | assignmentId, lockerId, session/rental ref | Realtime/notification |
| `facility.locker.release-requested.v1` | Facility | assignmentId, lockerId, commandId | Locker adapter |
| `training.enrollment.confirmed.v1` | Training | enrollmentId, offeringId, memberId | Notification/reporting |
| `training.waitlist.slot-offered.v1` | Training | entryId, offeringId, expiresAt | Notification |
| `training.session.cancelled.v1` | Training | sessionId, reason, affected count | Notification/commerce task |
| `training.attendance.finalized.v1` | Training | sessionId, counts, finalizedAt | Reporting |
| `platform.export.completed.v1` | Reporting | jobId, ownerId, expiresAt | Notification |

## 4. Event data rules

- Amount kèm currency; timestamps RFC 3339 UTC.
- Branch/zone ID có trong event cần scope/reporting.
- Không phát full entity snapshot mặc định; chỉ field ổn định mà consumer cần.
- Update event ghi changed fields hoặc semantic outcome, không phát raw old/new PII.
- `reasonCode` dùng catalog enum; free-text reason ở audit, không broadcast nếu không cần.

## 5. Compatibility

- Trong `.v1`, chỉ thêm optional field; consumer phải ignore unknown field.
- Không đổi type/meaning/requiredness của field hiện có.
- Breaking payload tạo `.v2`, publish song song trong migration window.
- Schema lưu trong repo và compatibility check ở CI khi implementation bắt đầu.
- Event đã publish không được mutate/reuse ID với payload khác.

## 6. Failure handling

- Retry exponential backoff + jitter.
- Sau threshold, chuyển DLQ/manual review nhưng outbox intent còn truy vết.
- Alert theo oldest unprocessed age, không chỉ queue length.
- Replay theo event ID/range, consumer vẫn dedupe.
- Poison event phải có schema/error metadata, không log sensitive payload toàn bộ.

