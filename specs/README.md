# Bộ đặc tả SunSwim

Phiên bản: `1.0-academic-baseline`  
Ngày tái cấu trúc: `2026-09-09`  
Ngôn ngữ chuẩn: tiếng Việt; mã, enum và API dùng tiếng Anh.

## 1. Mục đích

Bộ tài liệu tách BRS-FSD gốc thành các file nhỏ để người đọc dễ tìm và quản lý. Mỗi file có mục đích và chủ sở hữu rõ ràng. Nội dung được chia thành năm lớp:

1. Business: mục tiêu, phạm vi, năng lực, actor, quy trình và quy tắc.
2. Architecture: ranh giới hệ thống, domain, dữ liệu, transaction, event, bảo mật và vận hành.
3. Functional: hành vi chi tiết của từng module, trạng thái, validation, luồng và acceptance criteria.
4. Contracts: quy ước API, danh mục endpoint, event và lỗi.
5. Delivery: MVP, kiểm thử, rủi ro và điều kiện sẵn sàng.

Tài liệu nguồn: [SunSwim - Detailed Business & Functional Specification (BRS-FSD).md](</home/vuthanhnguyen/Downloads/SunSwim – Detailed Business & Functional Specification (BRS-FSD).md>).

## 2. Thứ tự đọc theo vai trò

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

## 4. Thứ tự ưu tiên tài liệu

Khi có mâu thuẫn, ưu tiên theo thứ tự:

1. Quyết định đã được duyệt trong `00-governance/03-assumptions-decisions-open-questions.md`.
2. Business rule có ID trong `01-business/05-business-rules-catalog.md`.
3. Functional spec của module.
4. Contract API/event.
5. BRS-FSD gốc.

Contract không được thêm hành vi trái với business rule. Nếu code và spec khác nhau, nhóm phải tạo change request thay vì tự sửa một phía.

## 5. Trạng thái quyết định

| Trạng thái | Ý nghĩa |
|---|---|
| `SOURCE_CONFIRMED` | Có trong BRS-FSD gốc |
| `BASELINED` | Giả định đã được nhóm chốt để lập kế hoạch và viết báo cáo |
| `DEFERRED` | Chủ động ngoài phạm vi release hiện tại |

## 6. Các baseline chính

- Hệ thống dùng modular monolith, một PostgreSQL cluster và process riêng cho worker/scheduler.
- Check-in, consume pass, presence và capacity nằm trong cùng transaction.
- Tích hợp bất đồng bộ dùng transactional outbox. Delivery ít nhất một lần và consumer phải idempotent.
- Gate trong MVP dùng fail closed. Lễ tân chỉ được override khi có quyền và phải ghi lý do.
- Database lưu thời gian bằng `timestamptz`. Lịch được tính theo timezone của branch và khoảng thời gian dùng dạng `[start, end)`.
- API biểu diễn VND bằng số nguyên; PostgreSQL dùng `numeric(19,0)`.
- API dùng REST `/api/v1`, lỗi theo RFC 9457. Các mutation quan trọng cần idempotency key.
- Báo cáo doanh thu trong MVP dùng cash basis: payment đã settled trừ refund đã settled.

Nhóm đã chốt các baseline này để làm báo cáo học phần. Khi triển khai thật, Product Owner và owner chuyên môn phải xác nhận lại trong workshop và xử lý thay đổi theo Change Control Process.

## 7. Definition of specification-ready

Một feature chỉ được đưa vào sprint khi có đủ:

- actor và quyền;
- precondition;
- happy path và exception quan trọng;
- business rule có ID;
- state transition nếu có vòng đời;
- dữ liệu/nguồn dữ liệu chuẩn;
- API/event bị tác động;
- acceptance criteria kiểm thử được;
- quyết định P0 liên quan đã đóng.

## Thuật ngữ cần biết

| Thuật ngữ | Giải thích dễ hiểu |
|---|---|
| Specification hoặc spec | Tài liệu mô tả hệ thống phải làm gì và phải đáp ứng điều kiện nào. |
| Baseline | Phiên bản đã được chốt để mọi người cùng dùng làm mốc. |
| API | Cách các phần mềm trao đổi dữ liệu và gọi chức năng của nhau. |
| Idempotency | Gửi lại cùng một yêu cầu nhiều lần nhưng hệ thống chỉ tạo tác động một lần. |
| Source of truth | Nguồn dữ liệu được chọn làm căn cứ chính thức khi các nguồn khác nhau. |
