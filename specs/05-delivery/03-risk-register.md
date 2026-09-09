# Risk Register

## 1. Phương pháp

Likelihood (L) và Impact (I) được chấm từ 1 đến 5. Score bằng `L x I`. Mức Cao từ 15 đến 25, Trung bình từ 8 đến 14, Thấp từ 1 đến 7. Risk owner theo dõi trigger và thực hiện phương án đã duyệt. Nguyên duy trì register và review hằng tuần trong vai trò người phụ trách Chương 8.

## 2. Danh sách rủi ro

| ID | Rủi ro | L | I | Score | Owner | Ứng phó chính | Trigger |
|---|---|---:|---:|---:|---|---|---|
| R-001 | Race gây double consume hoặc over-capacity | 4 | 5 | 20 | Backend Lead | Giảm: transaction, lock order, constraint và concurrency test | Số dư âm, capacity drift hoặc session trùng |
| R-002 | Payment settled nhưng fulfillment mất hoặc trùng | 4 | 5 | 20 | Commerce Lead | Giảm: outbox, idempotency, reconciliation và replay tool | Paid item pending trên 5 phút hoặc resource trùng |
| R-003 | Mất kết nối chi nhánh làm gate dừng do fail closed | 4 | 4 | 16 | Infrastructure Lead | Giảm: hai đường mạng, health dashboard và manual entry runbook | Device heartbeat mất trên 30 giây |
| R-004 | Software trả ALLOW nhưng cổng vật lý không mở | 3 | 5 | 15 | Device Integration Lead | Giảm: ack 10 giây, tự hoàn lượt và kiểm thử tại hiện trường | ALLOW không có OPENED ack |
| R-005 | Mô hình lớp không bao phủ cả course và drop-in | 3 | 4 | 12 | Solution Architect | Tránh: dùng Definition, Offering và Session trước Release 3 | Xuất hiện ngoại lệ không biểu diễn được |
| R-006 | Xử lý guardian hoặc waiver không đúng | 3 | 5 | 15 | Product Owner | Giảm: policy dưới 16 tuổi, legal review và UAT riêng | Có kế hoạch mở lớp trẻ em |
| R-007 | Thuế, refund hoặc receipt sai quy định | 3 | 5 | 15 | Finance Lead | Giảm: business rule đã chốt, finance review và test case biên | Chênh lệch receipt với settlement |
| R-008 | Retention hoặc export dữ liệu cá nhân không phù hợp | 3 | 5 | 15 | Security Lead | Giảm: classification, masking, retention job và access audit | Production PII export được bật |
| R-009 | Dynamic pricing tạo giá khó giải thích | 3 | 4 | 12 | Product Owner | Giảm: version rule, snapshot quote và golden test | Nhiều rule cùng khớp một giao dịch |
| R-010 | Báo cáo không khớp provider hoặc dữ liệu vận hành | 4 | 4 | 16 | Data Lead | Giảm: metric dictionary, daily reconciliation và drill-down | Chênh lệch vượt 0,1% sau batch ngày |
| R-011 | Cache hoặc projection bị dùng như source of truth | 3 | 5 | 15 | Solution Architect | Tránh: architecture guardrail và failure test khi mất cache | Kết quả nghiệp vụ thay đổi sau khi clear cache |
| R-012 | RBAC chỉ kiểm tra ở UI hoặc rò dữ liệu chéo chi nhánh | 3 | 5 | 15 | Security Lead | Giảm: backend policy, authorization matrix và IDOR test | Truy vấn chéo branch thành công |
| R-013 | Outbox hoặc DLQ backlog không được xử lý | 3 | 4 | 12 | DevOps Lead | Giảm: age alert, runbook, dashboard và replay có kiểm soát | Oldest message age vượt 5 phút |
| R-014 | Scheduler chậm làm freeze hoặc expiry sai | 3 | 4 | 12 | Backend Lead | Giảm: tính theo effective time, job idempotent và lag alert | Trạng thái khác effective time |
| R-015 | Trạng thái locker vật lý khác trạng thái logic | 4 | 3 | 12 | Device Integration Lead | Giảm: command log, ack, trạng thái pending và reconciliation | Command timeout hoặc thiết bị báo mismatch |
| R-016 | Scope tăng do thêm feature ngoài baseline | 4 | 4 | 16 | Project Manager | Tránh: deferred backlog, change control và ưu tiên MoSCoW | Có feature mới đi thẳng vào sprint |
| R-017 | Dự báo tải sai làm hạ tầng thiếu năng lực | 3 | 4 | 12 | Solution Architect | Giảm: thu forecast, performance test và capacity review | CPU, DB pool hoặc latency vượt 70% ngưỡng |
| R-018 | Log hoặc audit chứa secret và PII quá mức | 3 | 5 | 15 | Security Lead | Giảm: field allowlist, redaction và security test | Token, signature hoặc dữ liệu nhạy cảm xuất hiện trong log |
| R-019 | Dữ liệu cũ thiếu, trùng hoặc không đối soát được | 4 | 5 | 20 | Migration Lead | Giảm: profiling sớm, mapping, ba lần rehearsal và báo cáo sai lệch | Sai lệch trên 0,1% hoặc thiếu khóa liên kết |
| R-020 | Thiết bị hoặc provider giao chậm làm trượt pilot | 4 | 4 | 16 | Procurement Lead | Chuyển giao và giảm: SLA hợp đồng, mốc nghiệm thu, thiết bị dự phòng | Trễ milestone mua sắm trên 5 ngày |
| R-021 | Key User không đủ thời gian cho UAT | 3 | 4 | 12 | Product Owner | Giảm: giữ lịch UAT từ đầu, có người thay thế tại mỗi branch | Vắng trên 20% phiên UAT |
| R-022 | Thiếu nhân sự chủ chốt trong giai đoạn tích hợp | 3 | 4 | 12 | Project Manager | Giảm: backup owner, tài liệu bàn giao và knowledge sharing | Vắng trên 5 ngày hoặc bus factor bằng 1 |

## 3. Contingency cho rủi ro mức Cao

- R-001 hoặc R-002: dừng release, chuyển traffic về luồng quầy có kiểm soát, đối soát và sửa dữ liệu bằng script được duyệt.
- R-003 hoặc R-004: chuyển gate sang manual entry có ghi log, bố trí nhân viên kiểm tra pass tại quầy trong thời gian khắc phục.
- R-006 đến R-008: tạm tắt capability hoặc loại dữ liệu liên quan cho tới khi Product, Finance hoặc Security xác nhận.
- R-010: tạm dừng phát hành báo cáo tài chính, dùng báo cáo reconciliation có xác nhận của Finance.
- R-019: hoãn migration cutover, giữ hệ thống cũ ở chế độ read-only và chạy lại rehearsal.
- R-020: pilot bằng một bộ thiết bị đã nghiệm thu, không mở rộng ba chi nhánh cho tới khi lô còn lại đạt kiểm thử.

Chi phí cho các phương án trên lấy từ contingency reserve 1.151,40 triệu VND. Risk owner không tự giải ngân; PM duyệt trong cost baseline, Sponsor duyệt nếu phải dùng management reserve.

## 4. Opportunity Register

| ID | Cơ hội | Owner | Cách khai thác |
|---|---|---|---|
| OPP-001 | Dùng chung adapter cho gate và locker của nhiều model thiết bị | Solution Architect | Chuẩn hóa interface và thử với hai model trong UAT |
| OPP-002 | Pilot tốt có thể rút ngắn rollout hai chi nhánh còn lại | Project Manager | Chuẩn bị song song dữ liệu và đào tạo, chỉ kích hoạt khi exit gate đạt |
| OPP-003 | Managed service giảm effort vận hành và backup | DevOps Lead | So sánh TCO ba năm trong vendor evaluation |

## 5. Chu kỳ review

- Review hằng tuần trong cuộc họp trạng thái dự án.
- Review riêng trước mỗi release, migration rehearsal, pilot và go-live.
- Review bất thường sau incident, thay đổi nhà cung cấp hoặc change request lớn.
- Score từ 15 trở lên phải có owner, due date, trigger, contingency và residual assessment.
