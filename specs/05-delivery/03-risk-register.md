# Danh mục rủi ro dự án

## 1. Cách đánh giá

Khả năng xảy ra và mức ảnh hưởng được chấm từ 1 đến 5. Điểm rủi ro bằng khả năng nhân ảnh hưởng. Từ 15 đến 25 là Cao, từ 8 đến 14 là Trung bình, từ 1 đến 7 là Thấp. Vũ Thành Nguyên duy trì danh mục này và review hằng tuần khi thực hiện Chương 8.

## 2. Rủi ro ưu tiên

| ID | Rủi ro | Khả năng | Ảnh hưởng | Điểm | Mức | Owner | Phương án giảm nhẹ | Dấu hiệu kích hoạt |
|---|---|---:|---:|---:|---|---|---|---|
| R-001 | Internet giữa chi nhánh và máy chủ bị gián đoạn, gây tắc check-in | 4 | 5 | 20 | Cao | Phạm Tuấn Đạt | Dùng Offline/Local Cache tại máy quầy, lưu lịch sử quét và tự đồng bộ ngầm khi có mạng | Mất heartbeat, API không truy cập được hoặc hàng đợi cục bộ tăng |
| R-002 | Phát sinh yêu cầu ngoài SRS, chẳng hạn nhận diện khuôn mặt AI | 4 | 4 | 16 | Cao | Trần Nhật Nam | Dùng CCB, đánh giá tác động và ưu tiên chuyển yêu cầu sang giai đoạn 2 | Yêu cầu mới được đưa thẳng vào sprint hoặc làm thay đổi milestone |
| R-003 | Nhân viên lễ tân hoặc quản trị viên khó thích ứng với quy trình mới | 3 | 4 | 12 | Trung bình | Vũ Thành Công | Thiết kế sơ đồ tủ xanh/đỏ, lịch kéo thả; đào tạo trực tiếp và cung cấp video trước Go-live 2 tuần | Người dùng thao tác sai nhiều hoặc không hoàn thành kịch bản UAT |
| R-004 | Dữ liệu hội viên trong các file Excel bị thiếu, sai hoặc trùng khi chuyển đổi | 3 | 4 | 12 | Trung bình | Vũ Thành Công | Kiểm tra trùng số điện thoại, mã thẻ; chuyển đổi thử và đối soát song song | Số bản ghi lệch tăng hoặc không nối được hội viên với thẻ |
| R-005 | Hai lượt quét đồng thời làm dùng vé hai lần hoặc vượt sức chứa | 3 | 5 | 15 | Cao | Phạm Tuấn Đạt | Dùng transaction, khóa dữ liệu, idempotency và kiểm thử đồng thời | Số lượt âm, hai phiên mở cho một hội viên hoặc sức chứa lệch |
| R-006 | Hệ thống chấp nhận lịch dạy hoặc lịch học bị trùng | 3 | 5 | 15 | Cao | Vũ Thành Công | Kiểm tra khoảng thời gian ở ứng dụng và cơ sở dữ liệu, có test case cho mọi biên lịch | Một huấn luyện viên hoặc học viên có hai ca trùng giờ |
| R-007 | Local Cache cũ hoặc bị sửa làm kết quả ngoại tuyến không chính xác | 3 | 5 | 15 | Cao | Phạm Tuấn Đạt | Giới hạn dữ liệu, đặt thời hạn hiệu lực, bảo vệ máy quầy, kiểm tra toàn vẹn và đối soát sau đồng bộ | Cache quá hạn, hàng đợi bị thay đổi hoặc số liệu server xung đột |
| R-008 | Thiết bị RFID/QR hoặc cửa xoay không tương thích với phần mềm | 3 | 4 | 12 | Trung bình | Phạm Tuấn Đạt | Thử adapter sớm trên thiết bị thật và giữ phương án xử lý tại quầy | Đọc thẻ không ổn định, phản hồi thiết bị sai hoặc trễ |
| R-009 | Tải giờ cao điểm làm API vượt 1,5 giây hoặc luồng quầy vượt 5 giây | 3 | 5 | 15 | Cao | Vũ Thành Nguyên | Kiểm thử 200 giao dịch đồng thời, theo dõi truy vấn chậm và tối ưu critical path | P95 hoặc tỷ lệ lỗi tăng gần ngưỡng trong test |
| R-010 | Nhóm bốn người thiếu thời gian, làm trễ milestone | 4 | 4 | 16 | Cao | Trần Nhật Nam | Chia gói việc nhỏ, review hằng tuần, có người review chéo và ưu tiên yêu cầu bắt buộc | Công việc critical path trễ hoặc SPI dưới 0,95 |
| R-011 | Lỗi nghiêm trọng được phát hiện muộn trước bàn giao | 3 | 5 | 15 | Cao | Vũ Thành Nguyên | Viết test case sớm, chạy test liên tục, quản lý severity và chặn release | Lỗi nghiêm trọng còn mở gần M3 hoặc M4 |
| R-012 | Chi phí dự kiến vượt tổng kinh phí 150 triệu VNĐ | 3 | 5 | 15 | Cao | Trần Nhật Nam | Theo dõi EVM, kiểm soát cam kết mua sắm và xin duyệt trước khi dùng dự phòng | CPI dưới 1,0 hoặc dự báo hoàn thành vượt ngân sách |

## 3. Phương án khi rủi ro xảy ra

- R-001: Chuyển ứng dụng quầy sang chế độ ngoại tuyến, hiển thị độ mới của cache, lưu toàn bộ lần quét và đồng bộ ngay sau khi có mạng. Trường hợp không đủ dữ liệu an toàn được xử lý thủ công có ghi nhận.
- R-002: Dừng đưa yêu cầu mới vào công việc đang làm, lập Change Request và trình CCB. Mặc định chuyển sang giai đoạn 2 nếu không ảnh hưởng mục tiêu bắt buộc.
- R-003: Bổ sung ca hướng dẫn ngắn tại quầy, dùng dữ liệu mô phỏng và cử người hỗ trợ trực tiếp trong pilot.
- R-004: Không dùng bộ dữ liệu lỗi để bàn giao. Quay lại bản trước, sửa quy tắc làm sạch, chạy lại chuyển đổi và lập báo cáo đối soát.
- R-005 hoặc R-006: Dừng nghiệm thu module liên quan, sửa logic và chạy lại toàn bộ test đồng thời hoặc test lịch.
- R-008: Dùng thiết bị dự phòng tương thích hoặc chuyển sang thao tác tại quầy trong phạm vi pilot.
- R-009: Tạm dừng chức năng báo cáo nặng, tối ưu truy vấn và kiểm thử lại trước khi mở UAT.
- R-010 đến R-012: Project Manager điều chỉnh ưu tiên, bảo vệ critical path và báo cáo đại diện Chủ đầu tư nếu ảnh hưởng mốc hoặc ngân sách.

## 4. Dự phòng chi phí

Baseline dành 10 triệu VNĐ cho các rủi ro đã nhận diện. Đây không phải khoản để tự động chi hết. Owner đề xuất cách dùng, Trần Nhật Nam đánh giá ảnh hưởng và theo dõi trong Cost Baseline. Khoản dự phòng quản lý 5 triệu VNĐ chỉ được sử dụng khi có phê duyệt của đại diện Chủ đầu tư.

## 5. Chu kỳ review

- Review mỗi tuần cùng báo cáo tiến độ và chi phí.
- Review riêng trước các mốc 05/09, 26/09, 30/10 và 25/11/2026.
- Review ngay khi phát sinh sự cố mạng, thay đổi phạm vi, lỗi chuyển đổi hoặc kết quả kiểm thử tải không đạt.
- Mỗi rủi ro phải có owner, dấu hiệu kích hoạt, hành động tiếp theo, hạn xử lý và trạng thái.
- Rủi ro đã xảy ra được chuyển thành issue; không giữ nguyên ở trạng thái dự báo.

## Thuật ngữ cần biết

| Thuật ngữ | Giải thích dễ hiểu |
|---|---|
| Risk Register | Bảng theo dõi rủi ro, mức độ, người phụ trách và cách xử lý. |
| Owner | Người chịu trách nhiệm theo dõi và thúc đẩy việc xử lý một rủi ro. |
| Trigger | Dấu hiệu cho biết rủi ro sắp hoặc đã xảy ra. |
| Issue | Vấn đề đã xảy ra và cần được xử lý, khác với rủi ro mới chỉ có khả năng xảy ra. |
| EVM | Phương pháp theo dõi đồng thời giá trị công việc, tiến độ và chi phí. |
