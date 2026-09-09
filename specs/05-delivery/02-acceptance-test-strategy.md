# Acceptance và test strategy

## 1. Test layers

| Layer | Mục tiêu | Bắt buộc |
|---|---|---|
| Unit/domain | Rule/state/calculation deterministic | Mọi BR/edge arithmetic |
| Database integration | Constraint, lock, transaction, migration | Pass/capacity/payment/schedule |
| API contract | Schema/status/problem/idempotency/RBAC | Mọi public/device endpoint |
| Event contract | Envelope, compatibility, duplicate/order | Mọi event consumer |
| Provider integration | Signature, timeout, retry, sandbox | Payment/device/notification |
| E2E | User value stream qua UI/API/worker | Critical journeys |
| Non-functional | Load, security, resilience, restore | Release gates |

## 2. Critical test matrix

| Scenario | Expected invariant | Test technique |
|---|---|---|
| Hai gate dùng pass còn 1 lượt | Một ALLOW, balance 0 | Concurrent integration test |
| Hai gate tranh chỗ cuối | Một ALLOW, occupancy = limit | Concurrent integration test |
| Same request retry N lần | Một side effect, same result | Property/replay test |
| Duplicate webhook/out-of-order | Một settlement, không lùi state | Contract + replay fixture |
| DB failure giữa access flow | Full rollback | Fault injection |
| Paid rồi fulfillment crash | Retry cấp đúng một entitlement | Worker crash/restart test |
| Freeze start/end exact boundary | Eligibility đúng `[start,end)` | Time-controlled unit/E2E |
| Coach/locker interval overlap | Database và API cùng chặn | Constraint integration |
| Branch scope tampering | 403/404, không data leak | Authorization matrix |
| SSE disconnect/reconnect | Snapshot đúng, không double apply | Browser integration |
| Projection replay | Same aggregate result | Rebuild test |
| Restore backup | RPO/RTO đo được | DR drill |

## 3. State transition coverage

Với mỗi aggregate:

- Test mọi transition hợp lệ.
- Test mọi transition bất hợp lệ quan trọng.
- Test terminal state không bị hồi sinh ngoài explicit override.
- Test optimistic version conflict.
- Test idempotent retry của transition.
- Test audit/outbox được commit cùng business state.

Áp dụng cho Member Pass, Freeze Request/Period, Access Session, Locker Assignment, Order, Payment, Refund, Fulfillment, Offering, Enrollment, Export Job.

## 4. Calculation fixtures

Version-controlled golden fixtures cho:

- activation/expiry theo ngày/timezone;
- frequency daily/weekly/monthly sau khi `OQ-004` đóng;
- freeze effective days/early resume;
- price additive/compound/exclusive group/rounding;
- order totals, split payment, partial refund;
- revenue basis, unique visitor, occupancy timeline.

Fixture phải gồm exact boundary, leap day/month-end và negative/zero amount cases phù hợp.

## 5. Security tests

- Authentication/session/MFA/revocation.
- BOLA/IDOR trên mọi path ID.
- Branch scope cho list, detail, aggregate, export và SSE.
- Privilege escalation và separation of duties.
- QR replay/guess/revoke/log leakage.
- Webhook signature/raw-body/replay/amount/currency/account mismatch.
- Rate-limit/abuse, upload type/size/malware, export link access.
- Secret/PII absence trong logs/traces/errors.

Security release criteria bám baseline OWASP ASVS đã được Security owner chốt.

## 6. Performance/load profile

- Normal traffic + burst gate traffic theo assumptions NFR.
- Hot-member/hot-pass/hot-capacity-row contention.
- Report/export concurrent với gate path.
- Worker backlog catch-up sau outage.
- Provider latency/timeouts không giữ DB transaction.
- Đo P50/P95/P99, error rate, lock wait/deadlock, DB pool, outbox lag.

## 7. UAT packs

| Pack | Người duyệt | Nội dung |
|---|---|---|
| Front desk | Operations | Member, POS cash, gate exception, locker |
| Member | Product | Purchase/pass/freeze/class self-service |
| Pool safety | Operations/Safety | Capacity, override, outage fallback |
| Finance | Finance | Settlement, refund, receipt, reconciliation, export |
| Training | Training Manager | Offering, coach, enrollment, waitlist, attendance |
| Administration | Org Admin/Security | RBAC, branch scope, audit, config publish |

UAT evidence gồm test ID, actor, environment/build, input, expected/actual, screenshot/log reference và approver.

## 8. Defect release policy

- Block release: dữ liệu/tài chính sai, over-capacity, unauthorized access/data, duplicate fulfillment/payment, backup restore fail.
- Conditional: workaround an toàn, risk owner và expiry date rõ.
- Không “fix” test bằng cách nới invariant/spec mà không change approval.

