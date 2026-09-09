# Baseline dự án SunSwim dùng cho báo cáo học phần

## 1. Cách sử dụng baseline

Đây là bộ số liệu giả định cho một dự án triển khai thật tại chuỗi bể bơi SunSwim. Bốn thành viên Đạt, Công, Nam và Nguyên là nhóm lập báo cáo, không phải toàn bộ nhân sự phát triển hệ thống. Mỗi chương phải dùng chung các giả định dưới đây để tránh lệch số liệu.

| Thuộc tính | Baseline |
|---|---|
| Tên dự án | Xây dựng và triển khai hệ thống quản lý bể bơi SunSwim |
| Loại dự án | Phát triển phần mềm theo yêu cầu, tích hợp thiết bị và triển khai production |
| Phạm vi triển khai | 3 chi nhánh của cùng một doanh nghiệp |
| Thời gian | 01/10/2026 đến 31/07/2027, gồm 10 tháng |
| Sponsor | Ban Giám đốc SunSwim |
| Product Owner | Trưởng khối Vận hành SunSwim |
| Project Manager | Đạt, theo vai trò giả định trong báo cáo |
| Nhóm lập báo cáo | Đạt, Công, Nam, Nguyên |
| Người phê duyệt học thuật | Giảng viên phụ trách môn học |
| Phiên bản baseline | PMB-1.0 |

Các chức danh phía SunSwim được dùng theo vai trò, không tự đặt tên cá nhân khi đề bài chưa cung cấp.

## 2. Mục tiêu và điều kiện thành công

Đến ngày 31/07/2027, SunSwim có một hệ thống production dùng chung cho ba chi nhánh, thay thế việc quản lý rời rạc ở quầy, cổng ra vào, lớp học và báo cáo vận hành.

Các điều kiện thành công:

- 100% payment đã settled truy vết được tới order và quyền sử dụng được cấp.
- Không phát sinh pass âm lượt hoặc fulfillment trùng trong kiểm thử và giai đoạn pilot.
- Gate decision đạt P95 không quá 800 ms ở mức tải thiết kế của ba chi nhánh.
- Không vượt sức chứa do lỗi xử lý đồng thời của hệ thống.
- Không còn lỗi Severity 1 hoặc Severity 2 tại thời điểm go-live.
- Dữ liệu hội viên, pass đang hoạt động và số dư được đối soát trước khi chuyển đổi.
- Nhân viên vận hành thuộc ba chi nhánh được đào tạo và hoàn thành bài kiểm tra sử dụng.
- Hệ thống được pilot tại một chi nhánh trước khi mở rộng sang hai chi nhánh còn lại.

## 3. Phạm vi dự án

### Trong phạm vi

1. Khảo sát, chuẩn hóa quy trình và yêu cầu tại ba chi nhánh.
2. Member, guest, ba loại pass, lifecycle và usage ledger.
3. POS Cash, online payment, order, receipt, refund và fulfillment.
4. QR check-in, check-out, manual override và quản lý sức chứa.
5. Bảo lưu pass, locker cố định và locker tạm thời.
6. Lớp học, huấn luyện viên, enrollment, waitlist và attendance.
7. Opening hours, time slot, holiday rule và dynamic pricing.
8. Dashboard doanh thu, lượt vào, sức chứa và export có phân quyền.
9. Admin Web, Member responsive Web/PWA và Device API.
10. RBAC theo chi nhánh, audit log, notification, monitoring, backup và runbook.
11. Tích hợp thiết bị cổng, QR reader, locker controller và nhà cung cấp thanh toán qua adapter.
12. Migration dữ liệu, đào tạo người dùng, pilot và hỗ trợ sau go-live trong 30 ngày.

### Ngoài phạm vi giai đoạn này

- Multi-tenant SaaS cho nhiều doanh nghiệp độc lập.
- Payroll, hoa hồng huấn luyện viên, sổ cái và kế toán tổng hợp.
- Nhận diện khuôn mặt, sinh trắc học và native mobile app.
- Gate hoạt động offline bằng entitlement cache.
- Quản lý tồn kho bán lẻ đầy đủ.
- Đặt lane, đặt toàn bộ cơ sở và quản lý giải đấu.
- CRM marketing automation và lead pipeline.
- Lưu dữ liệu thẻ nhạy cảm hoặc tự thực hiện card acquiring.

## 4. Giải pháp và môi trường

| Thành phần | Baseline |
|---|---|
| Kiến trúc ứng dụng | Modular monolith, API và worker tách process |
| Frontend | Next.js và TypeScript |
| Backend | NestJS và TypeScript |
| Database | Managed PostgreSQL có backup và point-in-time recovery |
| API | REST/JSON theo `/api/v1` |
| Hạ tầng | Cloud production có load balancer, WAF và ít nhất 2 API instance |
| Tích hợp cổng | Device API và adapter của nhà cung cấp thiết bị |
| Thanh toán | Adapter kết nối một payment provider được chọn qua mua sắm |
| Realtime | Server-Sent Events cho dashboard sức chứa |
| Môi trường | Local, Dev, Staging/UAT và Production tách biệt |
| Vận hành | Centralized logs, metrics, traces, alerting và runbook |

PostgreSQL là nguồn dữ liệu chuẩn cho payment, pass, access và capacity. Redis chỉ dùng để tăng hiệu năng, không quyết định tính đúng đắn của giao dịch.

## 5. WBS cấp cao

| WBS | Work package | Đầu ra chính |
|---|---|---|
| 1.0 | Quản lý dự án | Charter, kế hoạch, báo cáo trạng thái, change log |
| 2.0 | Khảo sát và yêu cầu | Process map, BRS, backlog, acceptance baseline |
| 3.0 | Kiến trúc và UX | Kiến trúc, data model, API, prototype giao diện |
| 4.0 | Nền tảng và bảo mật | Identity, RBAC, audit, CI/CD, observability |
| 5.0 | Core Pool Operation | Member, Pass, POS Cash, Access, Capacity |
| 6.0 | Operational Expansion | Freeze, locker, online payment, pricing, reports |
| 7.0 | Training Services | Class, coach, enrollment, waitlist, attendance |
| 8.0 | Tích hợp và thiết bị | Gate, QR reader, locker, payment, notification |
| 9.0 | Dữ liệu và chuyển đổi | Làm sạch, mapping, migration, đối soát |
| 10.0 | Kiểm thử và nghiệm thu | Integration, system, security, performance, UAT |
| 11.0 | Triển khai và đào tạo | Pilot, rollout ba chi nhánh, training, hypercare |
| 12.0 | Đóng dự án | Bàn giao, nghiệm thu, lessons learned, quyết toán |

Mỗi chương dùng nguyên mã WBS cấp cao này. Owner có thể phân rã thành `5.1`, `5.2` và các cấp thấp hơn nhưng không đổi tên work package cấp 1.

## 6. Lịch cấp cao

| Giai đoạn | Thời gian | Nội dung chính | Milestone |
|---|---|---|---|
| Khởi động | 01/10 đến 15/10/2026 | Charter, stakeholder, governance | M1: Charter approved |
| Khảo sát và baseline yêu cầu | 16/10 đến 30/11/2026 | Quy trình, phạm vi, backlog, acceptance | M2: Scope baseline |
| Kiến trúc và thiết kế | 01/12/2026 đến 15/01/2027 | Kiến trúc, dữ liệu, API, UX, kế hoạch tích hợp | M3: Design baseline |
| Release 0 | 16/01 đến 28/02/2027 | Nền tảng, RBAC, audit, CI/CD, master data | M4: Foundation ready |
| Release 1 | 01/03 đến 15/04/2027 | Member, Pass, POS Cash, QR Access, Capacity | M5: Core feature complete |
| Release 2 | 16/04 đến 31/05/2027 | Freeze, locker, online payment, pricing, report | M6: Expansion feature complete |
| Release 3 | 01/05 đến 15/06/2027 | Class, coach, enrollment, attendance | M7: Training feature complete |
| Kiểm thử và UAT | 01/06 đến 30/06/2027 | System, security, performance, migration rehearsal, UAT | M8: Go-live approved |
| Pilot và rollout | 01/07 đến 20/07/2027 | Pilot một chi nhánh, sau đó rollout hai chi nhánh | M9: Production rollout complete |
| Hypercare và đóng dự án | 21/07 đến 31/07/2027 | Theo dõi, sửa lỗi, bàn giao và quyết toán | M10: Project closed |

Một số giai đoạn chồng lấn có chủ đích. Chương 3 phải phân rã lịch chi tiết, xác định critical path và nêu rõ điều kiện để Release 3, kiểm thử và migration được chạy song song.

## 7. Nguồn lực dự án

Đội dự án thực tế gồm nhân sự nội bộ SunSwim, đội phát triển và nhà cung cấp chuyên môn. Nhóm delivery cốt lõi cao điểm khoảng 18 người, không tính Sponsor và 6 Key User tham gia bán thời gian.

| Vai trò | Số lượng kế hoạch | Trách nhiệm chính |
|---|---:|---|
| Sponsor | 1 | Cấp ngân sách, xử lý vấn đề vượt thẩm quyền PM |
| Product Owner | 1 | Ưu tiên yêu cầu, chấp nhận sản phẩm |
| Project Manager | 1 | Tích hợp kế hoạch, tiến độ, chi phí, thay đổi |
| Business Analyst | 2 | Khảo sát, yêu cầu, quy trình và UAT |
| Solution Architect | 1 | Kiến trúc, NFR, quyết định kỹ thuật |
| UI/UX Designer | 1 | Research, prototype và design system |
| Backend Developer | 4 | Domain, API, worker và integration |
| Frontend Developer | 3 | Admin Web, Member PWA và dashboard |
| QA Engineer | 3 | Test plan, automation, system test và UAT support |
| DevOps/SRE | 1 | CI/CD, cloud, monitoring, backup và release |
| Chuyên gia ngắn hạn | Theo nhu cầu | Security, database, device và payment integration |
| Key User | 6, mỗi chi nhánh 2 người | Review quy trình, UAT, đào tạo lại tại chi nhánh |

Bốn thành viên lập báo cáo vẫn chia chương theo [Bảng phân công](06-phan-cong-bao-cao-ptit.md). Chương 6 mô tả cơ cấu đội dự án thật ở trên, đồng thời ghi riêng trách nhiệm viết báo cáo của nhóm bốn người.

## 8. Baseline chi phí

Đơn vị trong các bảng là triệu VND, chưa bao gồm VAT. Đây là ROM estimate dùng cho lập kế hoạch, có độ chính xác mục tiêu từ -15% đến +25% trước khi có báo giá chính thức.

### 8.1 Chi phí nhân lực

| Vai trò hoặc nhóm việc | Cơ sở ước lượng | Thành tiền |
|---|---|---:|
| Product Owner phía doanh nghiệp | 0,5 FTE x 10 tháng x 50 | 250 |
| Project Manager | 1 người x 10 tháng x 60 | 600 |
| Business Analyst | 2 người x 8 tháng x 42 | 672 |
| Solution Architect | 1 người x 6 tháng x 70 | 420 |
| UI/UX Designer | 1 người x 5 tháng x 38 | 190 |
| Backend Developer | 4 người x 8 tháng x 45 | 1.440 |
| Frontend Developer | 3 người x 7 tháng x 42 | 882 |
| QA Engineer | 3 người x 7 tháng x 35 | 735 |
| DevOps/SRE | 1 người x 8 tháng x 55 | 440 |
| Chuyên gia security, database, device | 6 person-month x 65 | 390 |
| Đào tạo, migration và hỗ trợ vận hành | 8 person-month x 32 | 256 |
| Tổng chi phí nhân lực |  | **6.275** |

Đơn giá là loaded rate, gồm lương, bảo hiểm, thiết bị làm việc, quản lý doanh nghiệp và chi phí gián tiếp của đơn vị cung cấp nhân lực.

### 8.2 Hạ tầng, thiết bị và dịch vụ

| Hạng mục | Cơ sở ước lượng | Thành tiền |
|---|---|---:|
| Cloud, database, backup, WAF và monitoring | Dev, UAT, Production trong 10 tháng | 450 |
| Gate, QR reader, bộ điều khiển và lắp đặt | 3 chi nhánh, gồm thiết bị dự phòng | 1.200 |
| Locker controller và adapter tích hợp | 3 chi nhánh | 450 |
| POS terminal, máy in và phụ kiện mạng | 3 chi nhánh | 300 |
| Payment, SMS, email và phí thiết lập tích hợp | Gói triển khai ban đầu | 240 |
| Công cụ phát triển, kiểm thử và quản lý | License theo thời hạn dự án | 150 |
| Pentest và kiểm thử hiệu năng độc lập | 2 đợt đánh giá | 180 |
| Migration, đào tạo, đi lại và tài liệu | 3 chi nhánh | 250 |
| Pháp lý, hành chính và chi phí mua sắm | Gói dự toán | 100 |
| Tổng hạ tầng, thiết bị và dịch vụ |  | **3.320** |

### 8.3 Tổng ngân sách

| Thành phần | Giá trị |
|---|---:|
| Chi phí trực tiếp | 9.595,00 triệu VND |
| Contingency reserve, 12% chi phí trực tiếp | 1.151,40 triệu VND |
| Cost baseline | **10.746,40 triệu VND** |
| Management reserve, 7% chi phí trực tiếp | 671,65 triệu VND |
| Tổng ngân sách cần được cấp | **11.418,05 triệu VND** |

Cost baseline tương đương khoảng 10,75 tỷ VND. Tổng ngân sách được cấp tương đương khoảng 11,42 tỷ VND. Contingency reserve dùng cho known risks đã có trong risk register. Management reserve nằm ngoài cost baseline và chỉ Sponsor được quyền giải ngân.

## 9. Baseline mua sắm

| Nhóm mua sắm | Hình thức dự kiến | Người đề xuất | Người duyệt | Điều kiện nghiệm thu chính |
|---|---|---|---|---|
| Cloud và managed database | Thuê dịch vụ theo tháng | DevOps Lead | PM và Sponsor | HA, backup restore, monitoring và security test đạt |
| Gate, QR reader và controller | Hợp đồng trọn gói có lắp đặt | Technical Lead | PM và Sponsor | Device API, fail-closed, latency và pilot đạt |
| Locker controller | Trọn gói theo chi nhánh | Operations Lead | PM | Không cấp trùng, mở khóa và audit đạt |
| Payment provider | Hợp đồng dịch vụ theo giao dịch | Finance Lead | Sponsor | Sandbox E2E, webhook, refund và reconciliation đạt |
| Pentest độc lập | Fixed-price theo phạm vi | QA Lead | PM | Báo cáo hoàn chỉnh, không còn phát hiện Critical hoặc High chưa xử lý |
| Đào tạo và migration support | Time and materials có trần | BA Lead | PM | Đủ dữ liệu đối soát và người dùng hoàn thành đào tạo |

Nhà cung cấp được chấm theo đáp ứng kỹ thuật 35%, tổng chi phí sở hữu 25%, năng lực hỗ trợ 20%, thời gian cung cấp 10% và kinh nghiệm triển khai 10%. Hợp đồng từ 500 triệu VND trở lên cần Sponsor phê duyệt. Không ghi tên nhà cung cấp cụ thể trước khi hoàn thành vendor evaluation.

## 10. Giao tiếp và quản lý tài liệu

- Daily stand-up của đội triển khai kéo dài tối đa 15 phút.
- Họp trạng thái dự án vào chiều thứ Sáu hằng tuần, có tiến độ, chi phí, rủi ro và quyết định cần duyệt.
- Steering Committee họp hai tuần một lần và họp bất thường khi rủi ro mức Cao vượt thẩm quyền PM.
- Báo cáo tháng gửi Sponsor trong ba ngày làm việc đầu tiên của tháng kế tiếp.
- Blocker quá hai ngày làm việc phải được báo cho PM.
- Requirement, risk, issue, change request và decision phải có ID, owner, ngày đến hạn và trạng thái.
- Đạt quản lý mục lục, phiên bản và bản hợp nhất của báo cáo học phần.
- Mỗi thành viên chỉ sửa trực tiếp các chương mình phụ trách; dữ liệu dùng chung lấy từ baseline này.

## 11. Baseline chất lượng

| Chỉ tiêu | Ngưỡng chấp nhận |
|---|---|
| Acceptance test mức Critical | Pass 100% |
| Lỗi Severity 1 và Severity 2 tại go-live | 0 lỗi còn mở |
| Lỗi Severity 3 tại go-live | Không quá 10 lỗi, có workaround và lịch sửa |
| Gate decision | P95 không quá 800 ms ở tải thiết kế |
| Availability trong pilot | Từ 99,5% trong khung giờ vận hành |
| Migration | 100% bản ghi tài chính đối soát, sai lệch dữ liệu nghiệp vụ dưới 0,1% |
| Bảo mật | Không còn phát hiện Critical hoặc High từ pentest |
| Đào tạo | 100% key user hoàn thành, điểm kiểm tra từ 80% |
| Review tài liệu | Mỗi deliverable có người soạn, người review và người phê duyệt |

Công quản lý checklist Chương 9 và đối chiếu các chỉ tiêu này với test strategy, NFR, UAT và điều kiện go-live.

## 12. Baseline rủi ro

- Xác suất và tác động được chấm từ 1 đến 5.
- Điểm rủi ro bằng xác suất nhân tác động.
- Từ 15 đến 25 là Cao, từ 8 đến 14 là Trung bình, từ 1 đến 7 là Thấp.
- Rủi ro mức Cao phải có owner, trigger, phương án ứng phó và ngân sách dự phòng.
- Nguyên duy trì Risk Register cho Chương 8 và tổ chức review hằng tuần.
- Contingency reserve xử lý known risks; management reserve chỉ dùng cho thay đổi ngoài baseline.

## 13. Quyền duyệt và nghiệm thu

| Nội dung | Người chuẩn bị | Người review | Người chấp thuận |
|---|---|---|---|
| Charter và Project Management Plan | Project Manager | Product Owner | Sponsor |
| Phạm vi và business rule | Business Analyst | Project Manager | Product Owner |
| Kiến trúc, bảo mật và NFR | Solution Architect | Tech Lead, Security Lead | Architecture Board |
| Lịch và cost baseline | Project Manager | Finance Lead | Sponsor |
| Release và go-live | Release Manager | QA Lead, Operations Lead | Product Owner và Sponsor |
| Nghiệm thu sản phẩm | Project Manager | Product Owner | Sponsor |
| Bản báo cáo học phần | Đạt hợp nhất | Cả nhóm kiểm tra chéo | Giảng viên phụ trách |

## 14. Quyết định nghiệp vụ dùng chung

Các câu hỏi từng ghi Open hoặc Pending được chốt thành baseline học thuật tại [Giả định và quyết định baseline](../00-governance/03-assumptions-decisions-open-questions.md). Những quyết định này đủ để nhóm lập lịch, dự toán và viết báo cáo. Trước khi ký hợp đồng hoặc go-live thật, Product Owner vẫn phải xác nhận chúng trong workshop chính thức.

## 15. Kiểm soát thay đổi

1. Người đề xuất tạo change request và nêu phần baseline bị ảnh hưởng.
2. PM điều phối đánh giá tác động đến phạm vi, lịch, chi phí, nguồn lực, rủi ro và chất lượng.
3. Product Owner quyết định ưu tiên nghiệp vụ; Sponsor duyệt thay đổi làm tăng ngân sách hoặc lùi milestone cam kết.
4. Sau khi được duyệt, owner cập nhật tài liệu, backlog, lịch, ngân sách và risk register liên quan.
5. PM cập nhật version history và thông báo theo Communication Plan.

Sau M5, dự án ưu tiên đổi phạm vi theo nguyên tắc bỏ hoặc lùi hạng mục ít quan trọng trước khi kéo dài ngày go-live. Không sử dụng management reserve để che giấu việc vượt cost baseline.
