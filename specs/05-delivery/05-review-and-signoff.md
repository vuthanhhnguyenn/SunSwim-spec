# Kế hoạch review và xác nhận kết quả

## 1. Các vòng review chính

### Review 1: Yêu cầu và WBS

- Hạn: 05/09/2026.
- Người chuẩn bị: Vũ Thành Công và Phạm Tuấn Đạt.
- Người điều phối: Trần Nhật Nam.
- Người góp ý nghiệp vụ: Hoàng Thị Mai.
- Nội dung: SRS, phạm vi, WBS, tiêu chí chấp nhận, RFID/QR, Local Cache, chuyển đổi Excel và yêu cầu hiệu năng.

### Review 2: Kiến trúc và thiết kế

- Hạn: 26/09/2026.
- Người chuẩn bị: Phạm Tuấn Đạt và Trần Nhật Nam.
- Người review nghiệp vụ: Vũ Thành Công.
- Người review khả năng kiểm thử: Vũ Thành Nguyên.
- Nội dung: kiến trúc Java Spring Boot, React hoặc Vue, PostgreSQL, ERD, DFD, sơ đồ đối tượng, UI Prototype, bảo mật và đồng bộ ngoại tuyến.

### Review 3: Sẵn sàng UAT

- Hạn: 30/10/2026.
- Người điều phối kỹ thuật: Phạm Tuấn Đạt.
- Người xác nhận chất lượng: Vũ Thành Nguyên.
- Người dùng đại diện: Hoàng Thị Mai.
- Nội dung: bán vé, check-in, tủ đồ, xếp lịch, chuyển đổi thử và bằng chứng kiểm thử.

### Review 4: Bàn giao

- Hạn: 25/11/2026.
- Người chủ trì: Trần Nhật Nam.
- Người nghiệm thu phía khách hàng: Nguyễn Văn Bình và Hoàng Thị Mai theo trách nhiệm trong Tôn chỉ.
- Nội dung: hệ thống, mã nguồn, kế hoạch chuyển đổi, hướng dẫn lễ tân và quản lý, biên bản UAT, chi phí và vấn đề còn lại.

## 2. Điều kiện sẵn sàng phát triển

Một yêu cầu chỉ được đưa vào cài đặt khi:

- Có mục tiêu, actor, phạm vi và mức ưu tiên rõ.
- Có business rule, luồng chính, ngoại lệ và tiêu chí chấp nhận.
- Đã xác định ảnh hưởng đến dữ liệu, API, bảo mật, Local Cache hoặc thiết bị.
- Không còn câu hỏi làm thay đổi kiến trúc hoặc ước lượng.
- Có người thực hiện, người review và dữ liệu kiểm thử.

## 3. Điều kiện hoàn thành chức năng

- Mã nguồn và cấu hình đã được review.
- Test chức năng, tích hợp và test case rủi ro liên quan đều đạt.
- Phân quyền, audit và xử lý lỗi được kiểm tra.
- Tài liệu yêu cầu, API và hướng dẫn sử dụng đã cập nhật.
- Không tạo dữ liệu trùng khi request hoặc bản ghi ngoại tuyến được gửi lại.
- Có bằng chứng chạy trên môi trường tích hợp.

## 4. Điều kiện nghiệm thu dự án

- Hoàn thành 100% yêu cầu bắt buộc trong SRS.
- Không còn lỗi nghiêm trọng trước Go-live.
- 100% người dùng thử nghiệm ký xác nhận UAT.
- Check-in và gán tủ dưới 5 giây cho mỗi khách.
- API không quá 1,5 giây khi kiểm thử ít nhất 200 giao dịch đồng thời.
- Không chấp nhận lịch học trùng và không thất thoát vé trong phạm vi kiểm thử nghiệm thu.
- Milestone không trễ quá 5 ngày làm việc và SPI không thấp hơn 0,95.
- Chi phí thực tế không vượt 150 triệu VNĐ và CPI không thấp hơn 1,0.
- Đủ mã nguồn, kế hoạch chuyển đổi, hướng dẫn sử dụng và biên bản UAT.

## 5. Xác nhận nội bộ cho báo cáo học phần

| Phần | Người soạn chính | Người review chéo | Trạng thái |
|---|---|---|---|
| Chương 1, 2, 7 | Phạm Tuấn Đạt | Vũ Thành Công | Cập nhật khi hoàn thành |
| Chương 3, 8 | Vũ Thành Nguyên | Trần Nhật Nam | Cập nhật khi hoàn thành |
| Chương 4, 5 | Trần Nhật Nam | Phạm Tuấn Đạt | Cập nhật khi hoàn thành |
| Chương 6, 9 | Vũ Thành Công | Vũ Thành Nguyên | Cập nhật khi hoàn thành |
| Bản hợp nhất | Trần Nhật Nam | Cả nhóm | Cập nhật trước khi nộp |

Xác nhận nội bộ chỉ chứng minh trách nhiệm làm báo cáo của nhóm. Nó không thay thế chữ ký nghiệm thu của phía SunSwim trong một dự án triển khai thực tế.

## 6. Quản lý nhận xét

- Mỗi nhận xét có người xử lý, hạn hoàn thành và trạng thái.
- Nhận xét làm thay đổi phạm vi, lịch hoặc chi phí phải được chuyển thành Change Request.
- Lỗi nghiêm trọng phải đóng trước khi ký UAT hoặc bàn giao.
- Các thay đổi sau review phải được kiểm tra lại ở tài liệu phụ thuộc, test case và ma trận truy vết.

## Thuật ngữ cần biết

| Thuật ngữ | Giải thích dễ hiểu |
|---|---|
| Review | Hoạt động kiểm tra nội dung để phát hiện thiếu sót hoặc mâu thuẫn trước khi phê duyệt. |
| Sign-off | Xác nhận chính thức rằng tài liệu hoặc sản phẩm đã đạt yêu cầu. |
| Go-live | Thời điểm hệ thống bắt đầu được dùng cho hoạt động thật. |
| Change Request | Phiếu ghi nhận một thay đổi cùng lý do và tác động dự kiến. |
| Quality gate | Nhóm điều kiện bắt buộc phải đạt trước khi chuyển sang giai đoạn tiếp theo. |
