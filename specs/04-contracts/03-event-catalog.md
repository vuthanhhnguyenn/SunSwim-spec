# Quy ước và danh mục event

## 1. Envelope

Integration event dùng JSON envelope theo CloudEvents 1.0.x:

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

Tên custom extension dùng chữ thường. `id` không đổi khi retry, và publish time không được thay thế business time của event.

## 2. Delivery semantics

- Event được gửi qua transactional outbox với cơ chế at-least-once delivery.
- Consumer dùng `(consumer,eventId)` trong inbox để dedupe.
- Hệ thống không bảo đảm global ordering. Khi cần kiểm soát thứ tự, consumer dùng subject và aggregate version.
- Consumer bỏ qua event trùng. Version cũ không được ghi đè state mới.
- Event không chứa secret, raw QR, payment token hoặc PII không cần thiết.

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
| `access.offline-scan.reconciled.v1` | Access | requestId, deviceId, observedAt, local/server result, resolution | Audit, operations |
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

- Amount phải đi kèm currency; timestamp dùng RFC 3339 UTC.
- Event phục vụ scope hoặc reporting phải có branch ID và zone ID phù hợp.
- Mặc định không phát full entity snapshot. Payload chỉ chứa các field ổn định mà consumer cần.
- Update event ghi changed field hoặc semantic outcome, không phát raw old/new PII.
- `reasonCode` dùng enum trong catalog. Free-text reason được lưu ở audit và không broadcast khi không cần.
- Event đối soát bản ghi ngoại tuyến không chứa raw RFID/QR hoặc dữ liệu cá nhân không cần thiết.

## 5. Compatibility

- Trong `.v1`, producer chỉ được thêm optional field; consumer phải bỏ qua unknown field.
- Không đổi type/meaning/requiredness của field hiện có.
- Breaking payload tạo `.v2`, publish song song trong migration window.
- Schema lưu trong repo và compatibility check ở CI khi implementation bắt đầu.
- Event đã publish không được sửa hoặc dùng lại ID với payload khác.

## 6. Failure handling

- Retry exponential backoff + jitter.
- Khi vượt retry threshold, event được chuyển sang DLQ hoặc manual review nhưng outbox intent vẫn phải truy vết được.
- Alert dựa trên tuổi của event chưa xử lý lâu nhất, không chỉ dựa vào queue length.
- Khi replay theo event ID hoặc range, consumer vẫn phải dedupe.
- Poison event cần có schema và error metadata. Log không được ghi toàn bộ sensitive payload.

## Thuật ngữ cần biết

| Thuật ngữ | Giải thích dễ hiểu |
|---|---|
| Event envelope | Khung dữ liệu chung bao quanh nội dung chính của event. |
| Producer | Thành phần tạo và phát event. |
| Consumer | Thành phần nhận và xử lý event. |
| Dedupe | Phát hiện và bỏ qua event đã được xử lý trước đó. |
| DLQ | Nơi giữ event lỗi sau khi đã retry quá số lần cho phép. |
