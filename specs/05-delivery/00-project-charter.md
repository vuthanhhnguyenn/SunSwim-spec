# Tôn chỉ dự án SunSwim

## 1. Thông tin học phần

| Nội dung | Thông tin |
|---|---|
| Cơ sở đào tạo | Học viện Công nghệ Bưu chính Viễn thông |
| Khoa | Công nghệ Thông tin |
| Học phần | Quản lý dự án phần mềm |
| Giảng viên hướng dẫn | ThS. Ngô Tiến Đức |

## 2. Thông tin chung của dự án

| Nội dung | Thông tin |
|---|---|
| Tên dự án | Hệ thống Quản lý Hoạt động Chuỗi Bể bơi SunSwim |
| Mã dự án | `SSMS-2026-BTL` |
| Thời gian | Từ 15/08/2026 đến 25/11/2026, gồm 14 tuần |
| Tổng kinh phí | 150.000.000 VNĐ |
| Nguồn vốn | Ngân sách chuyển đổi số nội bộ chuỗi SunSwim |
| Chủ đầu tư và đơn vị thụ hưởng | Công ty Cổ phần Thể thao và Giải trí SunSwim |
| Địa chỉ | Tầng 5, Tòa nhà SunSwim Complex, Cầu Giấy, Hà Nội |
| Đại diện bên A | Ông Nguyễn Văn Bình, Tổng Giám đốc |
| Đơn vị thi công | Nhóm sinh viên Lớp D23CNPM02, Học viện Công nghệ Bưu chính Viễn thông |
| Nhà quản lý dự án | Trần Nhật Nam, mã sinh viên B23DCCN592 |
| Email nhà quản lý dự án | `nhatnam.tran@ptit.edu.vn` |

## 3. Nhóm phát triển

| Thành viên | Mã sinh viên | Vai trò trong dự án | Email |
|---|---|---|---|
| Trần Nhật Nam | B23DCCN592 | Project Manager và System Architect | `nhatnam.tran@ptit.edu.vn` |
| Vũ Thành Công | B23DCCN103 | Business Analyst Lead và Back-end Developer | `thanhcong.vu@ptit.edu.vn` |
| Phạm Tuấn Đạt | B23DCCN145 | Technical Lead và Full-stack Developer | `tuandat.pham@ptit.edu.vn` |
| Vũ Thành Nguyên | B23DCCN617 | QA/QC Lead và Tester | `thanhnguyen.vu@ptit.edu.vn` |

## 4. Mục tiêu dự án

### 4.1 Cụ thể

Xây dựng một hệ thống phần mềm quản lý tập trung cho nhiều chi nhánh SunSwim. Hệ thống số hóa các quy trình bán vé lượt và thẻ tháng, phát hành thẻ hội viên RFID hoặc QR code, kiểm soát check-in và check-out qua cửa xoay, quản lý thuê tủ đồ, quản lý lịch dạy của huấn luyện viên bơi, điểm danh ca học và cung cấp dashboard cho ban quản trị.

### 4.2 Đo lường được

- Thời gian quét mã check-in và gán tủ đồ tại quầy dưới 5 giây cho mỗi khách.
- Giảm 100% tình trạng trùng lịch học bơi và thất thoát vé.
- Hệ thống chịu được ít nhất 200 giao dịch đồng thời trong giờ cao điểm.
- Thời gian phản hồi API không quá 1,5 giây.

### 4.3 Có thể thực hiện

Dự án dùng kiến trúc phân tầng trên nền web, Java Spring Boot cho backend, React hoặc Vue cho frontend và PostgreSQL cho cơ sở dữ liệu. Nhóm tận dụng máy tính tại quầy lễ tân cùng máy quét mã vạch hoặc RFID sẵn có của SunSwim.

### 4.4 Phù hợp nhu cầu

Hệ thống xử lý tình trạng ùn tắc trong mùa cao điểm, cải thiện trải nghiệm khách hàng và cung cấp dữ liệu theo thời gian thực để ban giám đốc ra quyết định kinh doanh.

### 4.5 Có thời hạn

Các pha Phân tích, Thiết kế, Cài đặt, Kiểm thử, Đào tạo và Bàn giao nghiệm thu phải hoàn thành trong 14 tuần, từ 15/08/2026 đến 25/11/2026.

## 5. Sản phẩm bàn giao và mốc thời gian

| TT | Sản phẩm bàn giao | Mốc thời gian |
|---:|---|---|
| 1 | Tài liệu đặc tả yêu cầu phần mềm và WBS chi tiết cho chuỗi SunSwim | Tuần 3, ngày 05/09/2026 |
| 2 | Tài liệu thiết kế kiến trúc hệ thống và cơ sở dữ liệu, gồm ERD, DFD, sơ đồ đối tượng và UI Prototype cho giao diện quầy và admin | Tuần 6, ngày 26/09/2026 |
| 3 | Hệ thống hoàn chỉnh để kiểm thử chấp nhận tại một cơ sở mẫu, gồm các module bán vé, check-in, tủ đồ và xếp lịch | Tuần 11, ngày 30/10/2026 |
| 4 | Hệ thống và bộ tài liệu bàn giao, gồm mã nguồn, kế hoạch chuyển đổi, hướng dẫn sử dụng cho lễ tân và quản lý, cùng biên bản nghiệm thu UAT | Tuần 14, ngày 25/11/2026 |

## 6. Tiêu chí đánh giá thành công

### 6.1 Tiến độ

- Dự án hoàn thành và bàn giao đúng hạn.
- Các milestone chính không trễ quá 5 ngày làm việc so với Schedule Baseline.
- Chỉ số SPI không thấp hơn 0,95.

### 6.2 Ngân sách

- Tổng chi phí thực tế không vượt quá 150.000.000 VNĐ.
- Chỉ số CPI không thấp hơn 1,0.

### 6.3 Chất lượng và phạm vi

- Hệ thống đáp ứng 100% yêu cầu bắt buộc trong SRS.
- Không còn lỗi nghiêm trọng trước Go-live.
- 100% người dùng thử nghiệm ký xác nhận nghiệm thu UAT.

## 7. Các bên liên quan chính

| TT | Họ tên và đơn vị | Vai trò | Trách nhiệm | Liên hệ |
|---:|---|---|---|---|
| 1 | Nguyễn Văn Bình, Công ty Cổ phần Thể thao và Giải trí SunSwim | Đại diện Chủ đầu tư | Phê duyệt Tôn chỉ dự án, cấp ngân sách, giải phóng tài nguyên, thẩm duyệt thay đổi phạm vi và nghiệm thu bàn giao | `binh.nguyen@sunswim.vn` |
| 2 | Trần Nhật Nam, B23DCCN592 | Project Manager | Lập kế hoạch, điều phối tiến độ, kiểm soát chi phí, rủi ro và thay đổi, đồng thời chủ trì các mốc nghiệm thu | `nhatnam.tran@ptit.edu.vn` |
| 3 | Vũ Thành Công, B23DCCN103 | Business Analyst Lead | Khảo sát quy trình SunSwim, soạn SRS và phân tích cơ sở dữ liệu cho các phân hệ nghiệp vụ | `thanhcong.vu@ptit.edu.vn` |
| 4 | Phạm Tuấn Đạt, B23DCCN145 | Technical Lead | Thiết kế kiến trúc, lựa chọn công nghệ, phân rã module mã nguồn, quản lý cấu hình Git và bảo mật dữ liệu | `tuandat.pham@ptit.edu.vn` |
| 5 | Vũ Thành Nguyên, B23DCCN617 | QA/QC Lead | Lập SQAP, viết test case, kiểm thử tải giờ cao điểm và điều phối UAT | `thanhnguyen.vu@ptit.edu.vn` |
| 6 | Hoàng Thị Mai, Công ty Cổ phần Thể thao và Giải trí SunSwim | Đại diện Khách hàng | Làm rõ yêu cầu nghiệp vụ, góp ý giao diện quầy lễ tân và trực tiếp kiểm thử nghiệm thu | `mai.hoang@sunswim.vn` |

## 8. Rủi ro chính và phương án xử lý

| TT | Rủi ro | Mức độ | Phương án giảm nhẹ |
|---:|---|---|---|
| 1 | Đường truyền Internet giữa các chi nhánh bị gián đoạn, gây tắc nghẽn khi quẹt thẻ check-in | Cao | Thiết kế Offline/Local Cache tại máy quầy lễ tân, lưu tạm lịch sử quét thẻ và tự động đồng bộ lên máy chủ khi có mạng trở lại |
| 2 | Phát sinh yêu cầu ngoài phạm vi, chẳng hạn tích hợp nhận diện khuôn mặt AI | Cao | Thành lập CCB; mọi yêu cầu ngoài SRS phải được đánh giá tác động đến chi phí và tiến độ, sau đó ưu tiên chuyển sang giai đoạn 2 |
| 3 | Nhân viên lễ tân hoặc quản trị viên khó thích ứng với quy trình mới | Trung bình | Thiết kế UI/UX trực quan bằng sơ đồ tủ đồ xanh hoặc đỏ và lịch ca bơi kéo thả; đào tạo trực tiếp và cung cấp video hướng dẫn trước Go-live 2 tuần |
| 4 | Dữ liệu hội viên cũ trong các file Excel phân tán bị sai khi chuyển đổi | Trung bình | Tự động kiểm tra trùng số điện thoại và mã thẻ; chạy thử chuyển đổi và đối soát song song trước khi bàn giao |

## 9. Phê duyệt

Tôn chỉ này xác nhận dự án được khởi động và trao cho Project Manager quyền huy động nhân lực, tài nguyên và sử dụng ngân sách đã được phê duyệt trong phạm vi, mục tiêu và thời hạn nêu trên.

| Đại diện Chủ đầu tư | Đại diện Đơn vị thi công |
|---|---|
| Nguyễn Văn Bình, Tổng Giám đốc Công ty Cổ phần SunSwim | Trần Nhật Nam, Nhà quản lý dự án |
| Ký và ghi rõ họ tên | Ký và ghi rõ họ tên |

## Thuật ngữ cần biết

| Thuật ngữ | Giải thích dễ hiểu |
|---|---|
| Project Charter hoặc Tôn chỉ dự án | Văn bản xác nhận dự án được bắt đầu và trao quyền cho Project Manager. |
| SRS | Tài liệu mô tả các yêu cầu phần mềm phải đáp ứng. |
| WBS | Cách chia dự án thành các nhóm công việc nhỏ để quản lý. |
| SPI | Chỉ số so sánh tiến độ thực hiện với tiến độ kế hoạch. |
| CPI | Chỉ số so sánh giá trị công việc hoàn thành với chi phí đã sử dụng. |
| UAT | Kiểm thử do đại diện người dùng thực hiện trước khi nghiệm thu. |
| CCB | Nhóm xem xét và phê duyệt hoặc từ chối các yêu cầu thay đổi. |

Nguồn: [Tôn chỉ dự án do nhóm cung cấp](/home/vuthanhnguyen/.codex/attachments/72117694-ff3f-49f5-b267-88a8ed5727ae/pasted-text.txt).
