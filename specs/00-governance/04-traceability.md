# Ma trận truy vết yêu cầu

Ma trận này ở mức capability để điều hướng. Mỗi functional spec chứa acceptance criteria chi tiết.

| Capability | Objective/BR gốc | Functional spec | Architecture/contract | Release |
|---|---|---|---|---|
| Member & Pass | `BR-MEM-001..008` | `03-functional/01-membership-passes.md` | Domain/data, API, error | R1 |
| Access & Capacity | `BR-GATE-001..008` | `03-functional/02-access-capacity.md` | Consistency/events, API | R1 |
| Freeze | `BR-FRZ-001..005` | `03-functional/03-freeze-lifecycle.md` | Domain/data, events | R2 |
| Classes | Module 4 rules | `03-functional/04-classes-coaches.md` | Domain/data, API/events | R3 |
| Locker | Module 5 rules | `03-functional/05-lockers.md` | Integration/events | R2 |
| Commerce | Module 6, `BR-CROSS-001/005/006` | `03-functional/06-commerce-payments.md` | Consistency/events, API | R1/R2 |
| Schedule & Pricing | Module 7 | `03-functional/07-scheduling-pricing.md` | Domain/data, API | R2 |
| Reporting | Module 8 | `03-functional/08-reporting.md` | Read models, export API | R1/R2 |
| Authorization | Module permissions, NFR 14.4/14.5 | All functional specs | Security, API | R1 |
| Audit | Common rule 3.2 | All mutation flows | Data/security/event | R1 |
| Notifications | Section 12 | Module-specific triggers | Event catalog | R1+ |

## Coverage gate

Một requirement được coi là “covered” khi có đủ bốn liên kết:

`Business rule → Functional behavior → Contract/data impact → Acceptance test`.

Các điểm cần xác nhận lại trước khi ký hợp đồng hoặc go-live:

- API schema đang là logical contract; binding cụ thể được bổ sung sau vendor selection.
- Tax đã có baseline giá gồm thuế; e-invoice nằm ngoài phạm vi giai đoạn này.
- Capacity kiểm soát đồng thời theo branch và pool zone; member chỉ có một open session toàn chuỗi.
- Guardian và waiver bắt buộc với member dưới 16 tuổi; household portal đầy đủ vẫn là deferred backlog.
