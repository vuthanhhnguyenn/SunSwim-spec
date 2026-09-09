# Review, Definition of Ready và sign-off

## 1. Kế hoạch review

### Review A: Business policy và phạm vi

Thành phần: Product Owner, Operations, Finance, Training, BA và PM.

Nội dung: pass, freeze, refund, class, locker, guardian, reporting và release scope. Bộ quyết định dùng cho lập kế hoạch đã được ghi tại [Giả định và quyết định baseline](../00-governance/03-assumptions-decisions-open-questions.md). Khi triển khai thật, workshop này xác nhận hoặc tạo change request, không mở lại quyết định bằng trao đổi miệng.

### Review B: Thiết bị, payment và vận hành

Thành phần: Operations, Device Vendor, Finance, Tech Lead, Security và Support.

Nội dung: device contract, payment contract, ack timeout, fail-closed, manual procedure, settlement và reconciliation. Review hoàn thành trước khi ký hợp đồng thiết bị và payment provider.

### Review C: Kiến trúc, bảo mật và NFR

Thành phần: Architect, Engineering, QA, DevOps/SRE, Security và Product Owner.

Nội dung: ADR, SLO, RPO, RTO, retention, tải thiết kế, topology, migration và go-live gate. Review hoàn thành trước M3: Design baseline.

## 2. Definition of Ready cho feature

- Objective và actor rõ.
- Rule, FR và AC có ID và trace.
- State, data, API và event impact được xác định.
- Quyết định business liên quan đã có baseline hoặc change request được duyệt.
- UX state cho error, empty, loading và permission được mô tả.
- Security, privacy và audit impact đã review.
- Test data, provider sandbox và dependency sẵn sàng.
- Estimate không chứa hành vi nghiệp vụ chưa xác định.

## 3. Definition of Done

- Code, migration và configuration đã review.
- Unit, integration, contract và E2E test theo risk đều đạt.
- OpenAPI và event schema được cập nhật, compatibility check đạt.
- Authorization, branch scope và audit được kiểm tra.
- Metrics, logs, traces, alerts và runbook có sẵn.
- Có migration, backfill và reconciliation plan nếu thay đổi dữ liệu.
- Spec, traceability và ADR được cập nhật.
- Có UAT evidence và sign-off của owner.
- Rollout và rollback-forward plan được duyệt.

## 4. Sign-off baseline dùng cho báo cáo

| Khu vực | Người chuẩn bị trong nhóm | Người xác nhận nội bộ | Trạng thái |
|---|---|---|---|
| Charter, phạm vi và business rule | Đạt | Cả nhóm | `BASELINED` ngày 09/09/2026 |
| Lịch và milestone | Nguyên | Đạt | `BASELINED` ngày 09/09/2026 |
| Chi phí và mua sắm | Nam | Đạt | `BASELINED` ngày 09/09/2026 |
| Nguồn lực và RACI | Công | Nam | `BASELINED` ngày 09/09/2026 |
| Risk Management Plan | Nguyên | Đạt | `BASELINED` ngày 09/09/2026 |
| Quality Management Plan | Công | Đạt | `BASELINED` ngày 09/09/2026 |
| Kiến trúc, dữ liệu, API và event | Nguyên, Nam | Công | `BASELINED` ngày 09/09/2026 |
| Security, privacy, SLO và DR | Công, Nguyên | Cả nhóm | `BASELINED` ngày 09/09/2026 |

Trạng thái trên xác nhận bộ giả định đủ để viết báo cáo học phần. Nếu SunSwim triển khai thật, Sponsor, Product Owner và các owner chuyên môn phải ký nghiệm thu theo bảng quyền duyệt trong [Baseline dự án](07-academic-project-baseline.md). Không dùng sign-off nội bộ của nhóm sinh viên để thay cho phê duyệt thương mại hoặc pháp lý.

## 5. Điều kiện gắn phiên bản

Phiên bản tài liệu có thể gắn nhãn `1.0-academic-baseline` khi:

1. Không còn chuỗi `Pending`, `TBD` hoặc câu hỏi Open ảnh hưởng phạm vi báo cáo.
2. Chín chương dùng cùng WBS, ngày, nhân lực và chi phí.
3. Mỗi chương đã được một thành viên khác review.
4. Owner xác nhận đã xử lý các nhận xét mức Blocker và Major.
5. Đạt hoàn thành mục lục, version history và bản hợp nhất.
