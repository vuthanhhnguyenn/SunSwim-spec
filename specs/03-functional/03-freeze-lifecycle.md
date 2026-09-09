# Functional Spec 03: Freeze và Pass Lifecycle

## 1. Mục tiêu

Cho phép tạm ngưng entitlement trong khoảng được phép, bảo toàn quyền lợi theo policy và chặn access chính xác kể cả scheduler chậm.

## 2. Actors

| Action | Manager | Receptionist | Member |
|---|---:|---:|---:|
| Create request | Yes | Branch | Own pass |
| Approve/reject | Yes | Conditional | No |
| Cancel pending | Yes | Yes | Own |
| Early resume | Yes | Conditional | Request |
| Override quota/dates | Permission + audit | No | No |

## 3. Freeze policy

Versioned policy snapshot/reference gồm:

- minimum/maximum effective days per request;
- maximum total days và request count per pass;
- advance notice/cutoff;
- allowed reasons/evidence required;
- fee/approval policy;
- early-resume quota behavior;
- timezone và cách tính ngày.

Policy update không đổi request đã approved trừ migration có audit.

## 4. State models

### Request

```mermaid
stateDiagram-v2
    [*] --> DRAFT
    DRAFT --> PENDING
    PENDING --> APPROVED
    PENDING --> REJECTED
    PENDING --> CANCELLED
    APPROVED --> CANCELLED: before effective start
    APPROVED --> COMPLETED: period ends
```

### Period effect

`SCHEDULED` khi approved nhưng chưa tới start; `ACTIVE` tại `[start, end)`; `COMPLETED` sau end; `ENDED_EARLY` nếu resume sớm.

Pass status hiển thị có thể là `SUSPENDED`, nhưng eligibility phải query effective period để chặn tại đúng mốc.

## 5. Date semantics

Nếu user chọn ngày 10/09 đến 19/09 “bao gồm cả hai ngày”:

```text
start_at = 2026-09-10 00:00 branch timezone
end_exclusive = 2026-09-20 00:00 branch timezone
effective_days = 10
```

Expiry adjustment ledger ghi `+10 calendar days`. UI hiển thị ngày kết thúc inclusive; API dùng `endExclusive` để không mơ hồ.

## 6. Request flow

1. Load pass, active policy và remaining entitlement.
2. Validate pass/product cho freeze, date order, min/max, notice và overlap.
3. Nếu evidence required, attachment phải upload private và scan trước approval.
4. Create `PENDING` với policy version và requested days.
5. Notify approver sau commit.

## 7. Approval flow

1. Actor xem pass usage, expiry, prior freeze và request details.
2. Backend revalidate pass version, quota, count, overlap, date và quyền.
3. Trong transaction: mark request approved, create period, append expiry adjustment, audit/outbox.
4. Nếu period đang effective, eligibility bị chặn ngay; không chờ job.
5. Retry approval idempotent trả cùng result; concurrent approver chỉ một thành công.

## 8. Reject/cancel/resume

- Reject bắt buộc reason; không tạo period/expiry adjustment.
- Cancel pending do member/staff theo quyền.
- Cancel approved chỉ trước start và theo policy; đảo expiry adjustment bằng ledger entry, không xóa.
- Early resume đặt actual `end_exclusive`, tính effective days thật và tạo delta correction.
- Early resume hoàn lại quota freeze chưa dùng, làm tròn theo ngày.

## 9. Validation/errors

`FREEZE_NOT_ALLOWED`, `FREEZE_DATE_INVALID`, `FREEZE_TOO_SHORT`, `FREEZE_TOO_LONG`, `FREEZE_NOTICE_REQUIRED`, `FREEZE_QUOTA_EXCEEDED`, `FREEZE_OVERLAP`, `EVIDENCE_REQUIRED`, `PASS_NOT_FREEZABLE`, `REQUEST_ALREADY_DECIDED`, `VERSION_CONFLICT`.

## 10. API impacts

- `GET/POST /api/v1/freeze-requests`
- `GET /api/v1/freeze-requests/{requestId}`
- `POST /api/v1/freeze-requests/{requestId}/decisions`
- `POST /api/v1/freeze-requests/{requestId}/cancellations`
- `POST /api/v1/freeze-periods/{periodId}/early-resumptions`
- `GET /api/v1/member-passes/{passId}/freeze-entitlement`

Decision request có `decision: APPROVE|REJECT`, `reason`, `expectedVersion`; dùng idempotency key.

## 11. UI

- Member: calendar chỉ cho ngày hợp lệ, preview effective days/new expiry/quota remaining.
- Manager queue: age, branch, member, evidence, current pass state, quota, conflict warnings.
- Decision modal: impact preview, reason, expected version.
- Timeline pass: request, period, suspension, expiry adjustments và early resume.

## 12. Acceptance criteria

- `AC-FRZ-001`: Two approved periods của cùng pass không overlap kể cả concurrent approval.
- `AC-FRZ-002`: At effective start, gate denies `PASS_SUSPENDED` kể cả scheduler chưa materialize status.
- `AC-FRZ-003`: 10 ngày inclusive user selection tạo `[start,end)` đúng 10 ngày và expiry +10 ngày.
- `AC-FRZ-004`: Approval retry không tạo period/adjustment thứ hai.
- `AC-FRZ-005`: Reject bắt buộc reason và expiry không đổi.
- `AC-FRZ-006`: Early resume tạo correction ledger/audit, không sửa mất history.
- `AC-FRZ-007`: Policy mới không tự đổi approved period cũ.

## 13. Quyết định baseline

Ngày freeze tính theo ngày lịch của chi nhánh. Early resume hoàn quota chưa dùng. Giai đoạn này không thu phí freeze nên không tạo order hoặc payment riêng. Dữ liệu freeze được giữ cùng vòng đời của pass và audit. Chi tiết truy vết tại `OQ-003`, `OQ-012` và `OQ-017`.
