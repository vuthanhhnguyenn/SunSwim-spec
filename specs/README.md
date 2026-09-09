# SunSwim Specification Suite

Phiên bản: `1.0-academic-baseline`  
Ngày tái cấu trúc: `2026-09-09`  
Ngôn ngữ chuẩn: tiếng Việt; mã, enum và API dùng tiếng Anh.

## 1. Mục đích

Bộ tài liệu này phân rã BRS-FSD gốc thành các tài liệu nhỏ, có chủ sở hữu và mục đích rõ ràng. Nội dung không chỉ được tách file mà còn được chuẩn hóa theo các lớp:

1. Business: mục tiêu, phạm vi, năng lực, actor, quy trình và quy tắc.
2. Architecture: ranh giới hệ thống, domain, dữ liệu, transaction, event, bảo mật và vận hành.
3. Functional: hành vi chi tiết của từng module, trạng thái, validation, luồng và acceptance criteria.
4. Contracts: quy ước API, danh mục endpoint, event và lỗi.
5. Delivery: MVP, kiểm thử, rủi ro và điều kiện sẵn sàng.

Tài liệu nguồn: [SunSwim – Detailed Business & Functional Specification (BRS-FSD).md](</home/vuthanhnguyen/Downloads/SunSwim – Detailed Business & Functional Specification (BRS-FSD).md>).

## 2. Cách đọc theo vai trò

| Vai trò | Đọc trước | Đọc tiếp |
|---|---|---|
| Product Owner/Business Owner | `01-business/01-business-context-scope.md` | Business rules, decision baseline, release plan |
| BA | Business context, process, rule catalog | Functional specs, traceability |
| Architect/Tech Lead | `02-architecture/02-solution-architecture.md` | Domain/data, consistency/events, ADR |
| Backend Developer | Functional module liên quan | API, event, error, data architecture |
| Frontend/UI/UX | Functional module liên quan | Roles, API/error catalog |
| QA/QC | Functional acceptance criteria | Traceability, test strategy, error catalog |
| DevOps/SRE | NFR/SLO | Deployment/operations, security |

## 3. Cấu trúc

```text
specs/
├── 00-governance/     # Quản trị tài liệu, thuật ngữ, quyết định, truy vết
├── 01-business/       # Bối cảnh, năng lực, actor, quy trình, rule, KPI
├── 02-architecture/   # Context, solution, domain/data, event, security, SLO, ADR
├── 03-functional/     # 8 module nghiệp vụ
├── 04-contracts/      # API, event, error contract
└── 05-delivery/       # MVP, kiểm thử, rủi ro, DoR/DoD
```

### Mục lục tài liệu

- Governance: [Document control](00-governance/01-document-control.md), [Glossary](00-governance/02-glossary.md), [Decision baseline](00-governance/03-assumptions-decisions-open-questions.md), [Traceability](00-governance/04-traceability.md).
- Business: [Context & scope](01-business/01-business-context-scope.md), [Capability/domain map](01-business/02-capability-domain-map.md), [Actors & RBAC](01-business/03-actors-rbac.md), [Business processes](01-business/04-business-processes.md), [Business rules](01-business/05-business-rules-catalog.md), [KPI/reporting](01-business/06-kpis-reporting.md).
- Architecture: [System context](02-architecture/01-system-context.md), [Solution architecture](02-architecture/02-solution-architecture.md), [Domain/data](02-architecture/03-domain-data-architecture.md), [Consistency/events](02-architecture/04-consistency-events.md), [Security/privacy](02-architecture/05-security-privacy.md), [NFR/SLO](02-architecture/06-nfr-slo.md), [Deployment/operations](02-architecture/07-deployment-operations.md), [ADRs](02-architecture/08-architecture-decisions.md).
- Functional: [Member & Pass](03-functional/01-membership-passes.md), [Access & Capacity](03-functional/02-access-capacity.md), [Freeze](03-functional/03-freeze-lifecycle.md), [Classes & Coaches](03-functional/04-classes-coaches.md), [Lockers](03-functional/05-lockers.md), [Commerce & Payments](03-functional/06-commerce-payments.md), [Scheduling & Pricing](03-functional/07-scheduling-pricing.md), [Reporting](03-functional/08-reporting.md).
- Contracts: [API conventions](04-contracts/01-api-conventions.md), [API catalog](04-contracts/02-api-catalog.md), [Event catalog](04-contracts/03-event-catalog.md), [Error catalog](04-contracts/04-error-catalog.md).
- Delivery: [Release plan](05-delivery/01-mvp-release-plan.md), [Test strategy](05-delivery/02-acceptance-test-strategy.md), [Risk register](05-delivery/03-risk-register.md), [References](05-delivery/04-references.md), [Review & sign-off](05-delivery/05-review-and-signoff.md), [Phân công báo cáo PTIT](05-delivery/06-phan-cong-bao-cao-ptit.md), [Baseline dự án thật](05-delivery/07-academic-project-baseline.md).

## 4. Quy tắc nguồn dữ liệu chuẩn

Khi có mâu thuẫn, ưu tiên theo thứ tự:

1. Quyết định đã được duyệt trong `00-governance/03-assumptions-decisions-open-questions.md`.
2. Business rule có ID trong `01-business/05-business-rules-catalog.md`.
3. Functional spec của module.
4. Contract API/event.
5. BRS-FSD gốc.

Contract không được tự tạo thêm hành vi trái business rule. Nếu code và spec khác nhau, tạo change request, không sửa âm thầm một phía.

## 5. Trạng thái quyết định

| Trạng thái | Ý nghĩa |
|---|---|
| `SOURCE_CONFIRMED` | Có trong BRS-FSD gốc |
| `BASELINED` | Giả định đã được nhóm chốt để lập kế hoạch và viết báo cáo |
| `DEFERRED` | Chủ động ngoài phạm vi release hiện tại |

## 6. Baseline quan trọng

- Kiến trúc: modular monolith, một PostgreSQL cluster, worker/scheduler tách process.
- Ranh giới nhất quán: check-in, consume pass, presence và capacity cùng transaction.
- Tích hợp bất đồng bộ: transactional outbox, delivery ít nhất một lần, consumer idempotent.
- Gate MVP: fail closed; lễ tân override theo quyền và bắt buộc lý do.
- Thời gian: lưu `timestamptz`, tính lịch theo timezone của branch, khoảng thời gian dùng dạng `[start, end)`.
- Tiền: VND dạng số nguyên ở API; PostgreSQL `numeric(19,0)`.
- API: REST `/api/v1`, lỗi theo RFC 9457, mutation quan trọng dùng idempotency key.
- Báo cáo doanh thu MVP: cash basis, chỉ tính payment đã settled trừ refund đã settled.

Các baseline trên đã được nhóm chốt cho mục đích học thuật. Nếu dự án được triển khai thật, Product Owner và owner chuyên môn phải xác nhận lại qua workshop và Change Control Process.

## 7. Definition of specification-ready

Một feature chỉ sẵn sàng vào sprint khi có đủ:

- actor và quyền;
- precondition;
- happy path và exception quan trọng;
- business rule có ID;
- state transition nếu có vòng đời;
- dữ liệu/nguồn dữ liệu chuẩn;
- API/event bị tác động;
- acceptance criteria kiểm thử được;
- quyết định P0 liên quan đã đóng.
