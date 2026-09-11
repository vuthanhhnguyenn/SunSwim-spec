# Kế hoạch bàn giao trong 14 tuần

## 1. Nguyên tắc

- Lịch bám đúng thời gian từ 15/08/2026 đến 25/11/2026.
- Mỗi giai đoạn phải tạo ra đầu ra có thể review, không chỉ báo cáo phần trăm hoàn thành.
- Bốn milestone trong Tôn chỉ là mốc kiểm soát chính thức.
- Bản chạy thử tại một cơ sở phải có đủ bán vé, check-in, tủ đồ và xếp lịch.
- Chất lượng, bảo mật, Local Cache, chuyển đổi dữ liệu và tài liệu hướng dẫn được làm song song với phát triển, không dồn hết về cuối.

## 2. Giai đoạn 1: Khởi động, khảo sát và yêu cầu

Thời gian: 15/08 đến 05/09/2026, tuần 1 đến tuần 3.

Đầu ra:

- Tôn chỉ dự án và danh sách bên liên quan.
- Quy trình hiện tại tại quầy, cửa xoay, tủ đồ và lớp học.
- SRS có yêu cầu chức năng, phi chức năng và tiêu chí chấp nhận.
- WBS chi tiết, ma trận truy vết và danh sách rủi ro ban đầu.

Điều kiện qua M1 ngày 05/09/2026:

- Phạm vi bắt buộc và ngoài phạm vi được phân biệt rõ.
- Các yêu cầu RFID/QR, Local Cache, chuyển đổi Excel và tải 200 giao dịch đồng thời đã có trong SRS.
- Hoàng Thị Mai đã review phần nghiệp vụ cần thiết.

## 3. Giai đoạn 2: Thiết kế hệ thống

Thời gian: 06/09 đến 26/09/2026, tuần 4 đến tuần 6.

Đầu ra:

- Kiến trúc web phân tầng và ranh giới module.
- Thiết kế PostgreSQL, ERD, DFD và sơ đồ đối tượng.
- Lựa chọn React hoặc Vue và prototype giao diện quầy, admin.
- Thiết kế API, bảo mật, Local Cache, đồng bộ ngoại tuyến và tích hợp thiết bị.
- Kế hoạch kiểm thử, chuyển đổi dữ liệu và mua sắm cần thiết.

Điều kiện qua M2 ngày 26/09/2026:

- Thiết kế bao phủ toàn bộ yêu cầu bắt buộc trong SRS.
- Luồng online và offline có cách chống ghi trùng, audit và đối soát.
- Prototype được đại diện khách hàng góp ý.
- Phạm vi cài đặt đủ rõ để chia module mã nguồn.

## 4. Giai đoạn 3: Cài đặt và tích hợp

Thời gian: 27/09 đến 23/10/2026, tuần 7 đến tuần 10.

Phạm vi phát triển:

- Hội viên, vé lượt, thẻ tháng và credential RFID/QR.
- Bán vé, ghi nhận thanh toán và cấp quyền sử dụng.
- Check-in/out, cửa xoay, sức chứa, Local Cache và đồng bộ.
- Tủ đồ tạm thời hoặc thuê theo chính sách.
- Huấn luyện viên, xếp lịch, ca học và điểm danh.
- Dashboard và các báo cáo vận hành chính.
- Phân quyền, audit và công cụ hỗ trợ chuyển đổi dữ liệu.

Cách thực hiện:

- Làm theo lát cắt hoàn chỉnh từ giao diện đến dữ liệu.
- QA viết và chạy test case ngay khi từng chức năng sẵn sàng.
- Tích hợp thiết bị bằng adapter để không gắn logic nghiệp vụ với một mẫu đầu đọc cụ thể.
- Cuối mỗi tuần có bản chạy được trên môi trường kiểm thử.

## 5. Giai đoạn 4: Hoàn thiện bản UAT tại cơ sở mẫu

Thời gian: 24/10 đến 30/10/2026, tuần 11.

Đầu ra:

- Bản phần mềm tích hợp đủ bốn module bắt buộc: bán vé, check-in, tủ đồ và xếp lịch.
- Dữ liệu mẫu đã được chuẩn bị; thiết bị tại cơ sở mẫu đã cấu hình.
- Test chức năng, tích hợp, đồng thời và ngoại tuyến đã đạt ngưỡng cho phép.
- Danh sách lỗi còn lại có mức độ, người xử lý và hạn hoàn thành.

Điều kiện qua M3 ngày 30/10/2026:

- Luồng check-in và gán tủ dưới 5 giây.
- API không quá 1,5 giây ở kịch bản 200 giao dịch đồng thời.
- Không chấp nhận lịch học trùng; không phát sinh vé dùng trùng trong kiểm thử.
- Không có lỗi nghiêm trọng ngăn UAT.

## 6. Giai đoạn 5: UAT, đào tạo và bàn giao

Thời gian: 31/10 đến 25/11/2026, tuần 12 đến tuần 14.

Công việc:

1. Chuyển đổi thử dữ liệu Excel, kiểm tra trùng số điện thoại và mã thẻ.
2. Chạy UAT tại một cơ sở mẫu với người dùng đại diện.
3. Đào tạo trực tiếp và cung cấp video hướng dẫn trước Go-live 2 tuần.
4. Sửa lỗi, chạy lại regression test và xác nhận các yêu cầu bắt buộc.
5. Đối soát dữ liệu, lịch sử quét và kết quả đồng bộ ngoại tuyến.
6. Hoàn thiện mã nguồn, kế hoạch chuyển đổi, hướng dẫn sử dụng và biên bản UAT.

Điều kiện qua M4 ngày 25/11/2026:

- 100% yêu cầu bắt buộc trong SRS đạt nghiệm thu.
- Không còn lỗi nghiêm trọng.
- 100% người dùng thử nghiệm ký UAT.
- Chi phí thực tế không vượt 150 triệu VNĐ và CPI không thấp hơn 1,0.
- Hồ sơ bàn giao đủ thành phần trong Tôn chỉ.

## 7. Phạm vi chuyển sang giai đoạn 2

- Nhận diện khuôn mặt hoặc sinh trắc học.
- Ứng dụng di động native.
- CRM marketing và tự động hóa bán hàng đầy đủ.
- Quản lý giải đấu, đặt làn bơi hoặc đặt toàn bộ cơ sở.
- Kế toán tổng hợp, tiền lương và hoa hồng huấn luyện viên.

Một yêu cầu chỉ được đưa ngược vào kế hoạch 14 tuần sau khi CCB đánh giá tác động và phê duyệt.

## Thuật ngữ cần biết

| Thuật ngữ | Giải thích dễ hiểu |
|---|---|
| Milestone | Mốc dùng để xác nhận một nhóm kết quả quan trọng đã hoàn thành. |
| SRS | Tài liệu mô tả hệ thống phải làm gì và đáp ứng điều kiện nào. |
| UAT | Đợt người dùng đại diện trực tiếp thử và xác nhận hệ thống. |
| Regression test | Kiểm thử lại để chắc rằng phần vừa sửa không làm hỏng chức năng cũ. |
| Adapter | Lớp kết nối giúp logic hệ thống không phụ thuộc trực tiếp vào một thiết bị hoặc nhà cung cấp. |
