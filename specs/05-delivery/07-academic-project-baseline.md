# Baseline dự án SSMS-2026-BTL

## 1. Mục đích sử dụng

File này chuyển Tôn chỉ dự án thành một bộ dữ liệu chung để cả chín chương báo cáo dùng thống nhất. Thông tin về tên dự án, thời gian, tổng kinh phí, thành viên, mục tiêu và mốc bàn giao được lấy từ [Tôn chỉ dự án](00-project-charter.md). Các bảng phân rã chi phí, WBS và cách tổ chức công việc là baseline kế hoạch do nhóm xây dựng trong giới hạn đã được phê duyệt.

## 2. Thông tin cố định

| Nội dung | Baseline |
|---|---|
| Tên dự án | Hệ thống Quản lý Hoạt động Chuỗi Bể bơi SunSwim |
| Mã dự án | `SSMS-2026-BTL` |
| Thời gian | 15/08/2026 đến 25/11/2026, gồm 14 tuần |
| Tổng kinh phí | 150.000.000 VNĐ |
| Nguồn vốn | Ngân sách chuyển đổi số nội bộ chuỗi SunSwim |
| Chủ đầu tư, đơn vị thụ hưởng | Công ty Cổ phần Thể thao và Giải trí SunSwim |
| Đại diện Chủ đầu tư | Nguyễn Văn Bình, Tổng Giám đốc |
| Đại diện khách hàng | Hoàng Thị Mai |
| Đơn vị thi công | Nhóm sinh viên Lớp D23CNPM02, PTIT |
| Project Manager | Trần Nhật Nam, B23DCCN592 |
| Giảng viên hướng dẫn | ThS. Ngô Tiến Đức |

## 3. Mục tiêu và tiêu chí thành công

Hệ thống quản lý tập trung hoạt động nhiều chi nhánh SunSwim, gồm bán vé lượt và thẻ tháng, thẻ hội viên RFID/QR, check-in/out, tủ đồ, lịch dạy, điểm danh và dashboard quản trị.

Các tiêu chí bắt buộc:

- Quét check-in và gán tủ tại quầy dưới 5 giây cho mỗi khách.
- Không còn trường hợp trùng lịch học bơi hoặc thất thoát vé do hệ thống.
- Chịu được ít nhất 200 giao dịch đồng thời trong giờ cao điểm.
- API phản hồi không quá 1,5 giây ở tải thiết kế.
- Các milestone chính không trễ quá 5 ngày làm việc; SPI không thấp hơn 0,95.
- Chi phí thực tế không vượt 150.000.000 VNĐ; CPI không thấp hơn 1,0.
- Đáp ứng 100% yêu cầu bắt buộc trong SRS.
- Không còn lỗi nghiêm trọng trước Go-live.
- 100% người dùng tham gia thử nghiệm ký xác nhận UAT.

## 4. Phạm vi cấp cao

### Trong phạm vi

1. Quản lý tập trung dữ liệu và nghiệp vụ nhiều chi nhánh.
2. Bán vé lượt, thẻ tháng và quản lý vòng đời quyền sử dụng.
3. Phát hành và kiểm tra thẻ hội viên RFID hoặc QR.
4. Check-in/out qua cửa xoay, kiểm soát sức chứa và xử lý ngoại lệ tại quầy.
5. Offline/Local Cache tại máy quầy, lưu lịch sử quét và đồng bộ khi có mạng trở lại.
6. Cấp, thuê, trả và theo dõi trạng thái tủ đồ.
7. Quản lý huấn luyện viên, lịch dạy, ca học, học viên và điểm danh.
8. Dashboard phục vụ quản lý và báo cáo vận hành.
9. Chuyển đổi dữ liệu hội viên cần thiết từ các file Excel hiện có.
10. Kiểm thử, đào tạo, chạy thử tại một cơ sở và bàn giao tài liệu.

### Ngoài phạm vi giai đoạn này

- Nhận diện khuôn mặt hoặc sinh trắc học.
- Hệ thống kế toán tổng hợp, tiền lương và hoa hồng huấn luyện viên.
- Ứng dụng di động native, CRM marketing đầy đủ và quản lý giải đấu.
- Thay mới toàn bộ máy tính, máy quét và hạ tầng sẵn có tại quầy.

Yêu cầu mới ngoài SRS phải đi qua CCB. Nhóm ưu tiên chuyển các yêu cầu không bắt buộc sang giai đoạn 2 thay vì tự mở rộng phạm vi trong 14 tuần.

## 5. Giải pháp kỹ thuật

| Thành phần | Baseline |
|---|---|
| Kiểu hệ thống | Ứng dụng web phân tầng, tổ chức theo modular monolith |
| Backend | Java Spring Boot |
| Frontend | React hoặc Vue; chốt lựa chọn trong mốc thiết kế tuần 6 |
| Cơ sở dữ liệu | PostgreSQL |
| Giao tiếp | REST API; hợp đồng chi tiết quản lý trong OpenAPI khi cài đặt |
| Thiết bị | Tận dụng máy tính quầy và máy quét mã vạch/RFID sẵn có |
| Ngoại tuyến | Local Cache và hàng đợi lịch sử quét tại máy quầy, tự đồng bộ khi có mạng |
| Bảo mật | Phân quyền theo vai trò, giới hạn dữ liệu theo chi nhánh, audit thao tác nhạy cảm |

PostgreSQL là nguồn dữ liệu chính thức. Local Cache chỉ hỗ trợ vận hành tạm thời khi mất mạng và phải được đối soát sau khi đồng bộ.

## 6. WBS cấp cao

| WBS | Gói công việc | Đầu ra chính | Người phụ trách chính |
|---|---|---|---|
| 1.0 | Quản lý dự án | Kế hoạch, lịch, chi phí, báo cáo trạng thái, thay đổi | Trần Nhật Nam |
| 2.0 | Khảo sát và yêu cầu | Quy trình, SRS, WBS chi tiết, tiêu chí chấp nhận | Vũ Thành Công |
| 3.0 | Kiến trúc, dữ liệu và UI | Kiến trúc, ERD, DFD, sơ đồ đối tượng, prototype | Phạm Tuấn Đạt và Trần Nhật Nam |
| 4.0 | Phát triển chức năng | Bán vé, hội viên, check-in, tủ đồ, xếp lịch, dashboard | Phạm Tuấn Đạt và Vũ Thành Công |
| 5.0 | Tích hợp và ngoại tuyến | RFID/QR, cửa xoay, Local Cache, đồng bộ và đối soát | Phạm Tuấn Đạt |
| 6.0 | Kiểm thử và UAT | SQAP, test case, kiểm thử tải, sửa lỗi, biên bản UAT | Vũ Thành Nguyên |
| 7.0 | Dữ liệu, đào tạo và chạy thử | Chuyển đổi Excel, hướng dẫn, đào tạo, pilot một cơ sở | Vũ Thành Công và Vũ Thành Nguyên |
| 8.0 | Bàn giao và đóng dự án | Mã nguồn, tài liệu, nghiệm thu, tổng kết | Trần Nhật Nam |

## 7. Lịch và mốc bàn giao

| Giai đoạn | Thời gian | Kết quả |
|---|---|---|
| Khởi động và khảo sát | 15/08 đến 05/09/2026 | Tôn chỉ, SRS và WBS chi tiết |
| Thiết kế | 06/09 đến 26/09/2026 | Kiến trúc, ERD, DFD, sơ đồ đối tượng và UI Prototype |
| Cài đặt và kiểm thử nội bộ | 27/09 đến 23/10/2026 | Các module cốt lõi đã tích hợp và có kết quả kiểm thử |
| Hoàn thiện bản chạy thử | 24/10 đến 30/10/2026 | Hệ thống sẵn sàng cho UAT tại một cơ sở mẫu |
| UAT, chuyển đổi và đào tạo | 31/10 đến 18/11/2026 | Biên bản UAT, dữ liệu đã đối soát, người dùng được hướng dẫn |
| Sửa lỗi và bàn giao | 19/11 đến 25/11/2026 | Mã nguồn, kế hoạch chuyển đổi, hướng dẫn và hồ sơ nghiệm thu |

| Milestone | Hạn | Điều kiện xác nhận |
|---|---|---|
| M1: SRS và WBS | 05/09/2026 | Phạm vi bắt buộc rõ, yêu cầu có tiêu chí chấp nhận |
| M2: Thiết kế hệ thống | 26/09/2026 | Đủ ERD, DFD, sơ đồ đối tượng, prototype và quyết định công nghệ |
| M3: Sẵn sàng UAT tại cơ sở mẫu | 30/10/2026 | Bán vé, check-in, tủ đồ và xếp lịch chạy được cùng nhau |
| M4: Bàn giao chính thức | 25/11/2026 | Đủ mã nguồn, kế hoạch chuyển đổi, hướng dẫn và biên bản UAT |

## 8. Nhóm dự án

| Thành viên | Vai trò dự án | Trách nhiệm chính |
|---|---|---|
| Trần Nhật Nam, B23DCCN592 | Project Manager và System Architect | Lập kế hoạch, điều phối, kiểm soát tiến độ, chi phí, rủi ro, thay đổi và chủ trì nghiệm thu |
| Vũ Thành Công, B23DCCN103 | Business Analyst Lead và Back-end Developer | Khảo sát nghiệp vụ, soạn SRS, phân tích dữ liệu và phát triển backend nghiệp vụ |
| Phạm Tuấn Đạt, B23DCCN145 | Technical Lead và Full-stack Developer | Thiết kế kỹ thuật, chọn nền tảng, phân rã module, quản lý Git, bảo mật và phát triển toàn hệ thống |
| Vũ Thành Nguyên, B23DCCN617 | QA/QC Lead và Tester | Lập SQAP, viết test case, kiểm thử tải, theo dõi lỗi và điều phối UAT |

Nguyễn Văn Bình phê duyệt ngân sách, thay đổi phạm vi và nghiệm thu bàn giao. Hoàng Thị Mai làm rõ nghiệp vụ, góp ý giao diện quầy và trực tiếp tham gia UAT.

## 9. Baseline chi phí

Đơn vị là triệu VNĐ. Tổng ngân sách 150 triệu là số liệu được duyệt trong Tôn chỉ. Phân bổ dưới đây là giả định kế hoạch để Chương 4 có đủ cơ sở theo dõi một dự án thật; thay đổi giữa các nhóm chi phí phải được Project Manager kiểm soát.

### 9.1 Chi phí trực tiếp

| Nhóm chi phí | Cơ sở lập kế hoạch | Số tiền |
|---|---|---:|
| Quản lý dự án và phân tích nghiệp vụ | Lập kế hoạch, khảo sát, SRS, WBS và báo cáo | 15 |
| Kiến trúc, dữ liệu và thiết kế giao diện | Kiến trúc, ERD, DFD, prototype | 15 |
| Phát triển phần mềm | Backend, frontend, cơ sở dữ liệu và dashboard | 50 |
| Kiểm thử và UAT | Test case, kiểm thử tích hợp, tải và hỗ trợ nghiệm thu | 15 |
| Chuyển đổi dữ liệu, đào tạo và tài liệu | Làm sạch Excel, chạy thử, hướng dẫn sử dụng | 10 |
| Hạ tầng và công cụ trong thời gian dự án | Môi trường phát triển, kiểm thử, lưu trữ và giám sát | 8 |
| Tích hợp RFID/QR và cửa xoay | Adapter, cấu hình và thử nghiệm thiết bị sẵn có | 15 |
| Bổ sung mạng, phụ kiện và thiết bị dự phòng nhỏ | Cáp, đầu đọc phụ, thiết bị hỗ trợ pilot | 7 |
| **Tổng chi phí trực tiếp** |  | **135** |

### 9.2 Dự phòng và tổng ngân sách

| Thành phần | Số tiền |
|---|---:|
| Chi phí trực tiếp | 135 |
| Dự phòng cho rủi ro đã nhận diện | 10 |
| **Cost Baseline** | **145** |
| Dự phòng quản lý | 5 |
| **Tổng kinh phí được duyệt** | **150** |

Dự phòng rủi ro dùng cho các tình huống đã có trong Risk Register. Dự phòng quản lý chỉ dùng khi có thay đổi chưa lường trước và phải được đại diện Chủ đầu tư chấp thuận. CPI được tính trên số liệu thực tế và phải không thấp hơn 1,0.

## 10. Mua sắm

Dự án ưu tiên tận dụng thiết bị sẵn có. Chỉ mua hoặc thuê những phần còn thiếu để hoàn thành pilot và bàn giao.

| Hạng mục | Hình thức dự kiến | Tiêu chí nghiệm thu |
|---|---|---|
| Hạ tầng chạy thử và lưu trữ | Thuê theo thời gian sử dụng | Ổn định, sao lưu được, đáp ứng tải kiểm thử |
| Thiết bị RFID/QR hoặc phụ kiện còn thiếu | Mua số lượng nhỏ | Đọc đúng thẻ, tương thích phần mềm và qua kiểm thử tại quầy |
| Hỗ trợ tích hợp cửa xoay | Theo phạm vi công việc cụ thể | Check-in/out, phản hồi thiết bị và ghi log đúng |
| Công cụ đào tạo, tài liệu | Tự thực hiện hoặc mua dịch vụ cần thiết | Người dùng pilot có đủ tài liệu và hoàn thành hướng dẫn |

Mọi cam kết mua sắm phải nằm trong tổng kinh phí 150 triệu. Không đưa tên nhà cung cấp vào baseline trước khi nhóm hoàn thành đánh giá và có phê duyệt.

## 11. Chất lượng và nghiệm thu

- Vũ Thành Nguyên quản lý SQAP, test case, báo cáo lỗi, kiểm thử tải và bằng chứng UAT.
- Kiểm thử hiệu năng phải chứng minh 200 giao dịch đồng thời, API không quá 1,5 giây và luồng check-in cùng gán tủ dưới 5 giây.
- Kiểm thử lịch phải chứng minh hệ thống không chấp nhận lịch học trùng.
- Kiểm thử vé phải bao phủ quét lặp, dùng đồng thời, hoàn tác và đồng bộ ngoại tuyến.
- Mọi yêu cầu bắt buộc trong SRS phải có ít nhất một test case và kết quả.
- Trước Go-live không được còn lỗi nghiêm trọng.
- Tất cả người dùng tham gia pilot phải ký UAT.

## 12. Giao tiếp và kiểm soát thay đổi

- Nhóm họp ngắn hằng ngày trong giai đoạn cài đặt và kiểm thử.
- Project Manager cập nhật tiến độ, chi phí, rủi ro và quyết định mỗi tuần.
- Blocker ảnh hưởng milestone phải được báo ngay cho Trần Nhật Nam.
- Yêu cầu ngoài SRS được ghi thành Change Request và đưa ra CCB.
- CCB đánh giá tác động đến phạm vi, thời gian, chi phí và chất lượng trước khi quyết định.
- Thay đổi làm vượt 150 triệu hoặc thay đổi phạm vi cấp cao cần Nguyễn Văn Bình phê duyệt.
- Tài liệu bị ảnh hưởng phải được cập nhật cùng traceability, lịch, chi phí và test case.

## 13. Bốn rủi ro ưu tiên

1. Mất Internet tại chi nhánh: dùng Local Cache, lưu lịch sử quét và tự đồng bộ khi có mạng.
2. Phạm vi tăng, chẳng hạn nhận diện khuôn mặt: CCB đánh giá và ưu tiên chuyển sang giai đoạn 2.
3. Nhân viên khó thích ứng: giao diện trực quan, sơ đồ tủ xanh/đỏ, lịch kéo thả, đào tạo và video trước Go-live 2 tuần.
4. Dữ liệu Excel sai hoặc trùng: kiểm tra số điện thoại, mã thẻ, chạy thử chuyển đổi và đối soát song song.

## Thuật ngữ cần biết

| Thuật ngữ | Giải thích dễ hiểu |
|---|---|
| Baseline | Phiên bản kế hoạch đã chốt để cả nhóm dùng chung và đo thay đổi. |
| Cost Baseline | Chi phí kế hoạch gồm chi phí trực tiếp và dự phòng cho các rủi ro đã biết. |
| WBS | Cách chia dự án thành các gói công việc có thể giao và theo dõi. |
| CCB | Nhóm xem xét yêu cầu thay đổi trước khi cho phép đưa vào dự án. |
| UAT | Đợt người dùng đại diện trực tiếp thử và xác nhận hệ thống đáp ứng nghiệp vụ. |
| SPI | Chỉ số cho biết tiến độ thực tế tốt hay kém hơn kế hoạch. |
| CPI | Chỉ số cho biết giá trị công việc thu được có tương xứng với chi phí đã dùng hay không. |
