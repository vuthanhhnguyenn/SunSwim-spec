# Giả định và quyết định baseline

## 1. Cách hiểu trạng thái

Các quyết định trong file này được chốt ở mức `BASELINED` để nhóm có đủ dữ liệu lập kế hoạch và viết báo cáo. Đây là giả định có kiểm soát của đồ án, không phải bằng chứng rằng đại diện SunSwim hoặc nhà cung cấp đã ký duyệt. Nếu dự án được triển khai thật, Product Owner phải xác nhận lại trong workshop và mọi thay đổi được xử lý qua Change Control Process.

## 2. Quyết định nền tảng

| ID | Quyết định baseline | Trạng thái | Cơ sở |
|---|---|---|---|
| DEC-001 | Một hồ sơ member dùng xuyên ba chi nhánh; số điện thoại chuẩn hóa là duy nhất khi có giá trị | `BASELINED` | Giảm hồ sơ trùng và hỗ trợ cross-branch |
| DEC-002 | Pass Product là mẫu sản phẩm; Member Pass giữ snapshot các điều khoản tại thời điểm phát hành | `BASELINED` | Thay đổi catalog không làm đổi quyền đã bán |
| DEC-003 | Một member chỉ có tối đa một access session đang mở trên toàn chuỗi | `BASELINED` | Ngăn dùng cùng QR tại nhiều chi nhánh |
| DEC-004 | Occupancy chuẩn bằng số access session mở có cờ `counts_toward_capacity` | `BASELINED` | Có thể audit và dựng lại từ dữ liệu gốc |
| DEC-005 | Khi mất mạng, máy quầy lễ tân dùng Offline/Local Cache để kiểm tra dữ liệu tối thiểu, lưu lịch sử quét và đồng bộ ngầm khi có mạng trở lại; yêu cầu không đủ dữ liệu an toàn phải bị từ chối hoặc chuyển sang xử lý thủ công có ghi nhận | `SOURCE_CONFIRMED` | Duy trì check-in có kiểm soát theo Tôn chỉ dự án |
| DEC-006 | Locker tạm thời là tùy chọn, không phải điều kiện bắt buộc để check-in | `BASELINED` | Hết locker không làm dừng toàn bộ luồng vào bể |
| DEC-007 | Giá được snapshot tại thời điểm tạo order; access restriction là policy riêng của pass | `BASELINED` | Tách tính tiền khỏi kiểm tra quyền sử dụng |
| DEC-008 | Payment webhook hoặc server verification là nguồn xác nhận thanh toán; redirect chỉ phục vụ UX | `SOURCE_CONFIRMED` | Tránh cấp quyền từ dữ liệu do client gửi |
| DEC-009 | Fulfillment chạy theo từng order item, có idempotency; order chỉ hoàn tất khi mọi item thành công | `BASELINED` | Cho phép retry và xử lý lỗi một phần |
| DEC-010 | Doanh thu vận hành tính theo cash basis: settled payment trừ settled refund | `SOURCE_CONFIRMED` | Khớp mục tiêu đối soát payment |
| DEC-011 | Hệ thống dùng modular monolith, API và worker tách process, chung PostgreSQL | `BASELINED` | Phù hợp quy mô ba chi nhánh và các transaction quan trọng |
| DEC-012 | API dùng REST/JSON `/api/v1`; lỗi theo RFC 9457 | `BASELINED` | Giữ contract nhất quán |
| DEC-013 | Timestamp lưu UTC; lịch và hiệu lực được tính theo timezone của chi nhánh | `BASELINED` | Tránh sai lệch ngày nghiệp vụ |
| DEC-014 | Tiền VND dùng số nguyên, không dùng floating point | `BASELINED` | Tránh sai số tính toán |
| DEC-015 | Event delivery ít nhất một lần; consumer phải idempotent | `BASELINED` | Retry có thể tạo bản tin trùng |

## 3. Kết quả chốt các câu hỏi ưu tiên cao

| ID | Quyết định baseline | Owner xác nhận khi triển khai thật |
|---|---|---|
| OQ-001 | Member không được đồng thời ở trạng thái `INSIDE` tại hai chi nhánh | Product Owner |
| OQ-002 | Pass toàn chuỗi dùng một quota chung, không tách quota theo chi nhánh | Product Owner |
| OQ-003 | Pass tính theo ngày hết hạn vào 23:59:59 theo timezone chi nhánh, không tính đủ 24 giờ từ lần check-in đầu | Product Owner |
| OQ-004 | Hạn mức tuần tính từ thứ Hai đến Chủ nhật; hạn mức tháng tính theo tháng dương lịch | Product Owner |
| OQ-005 | Sau 10 giây không nhận ack mở cổng, hệ thống đóng access session vừa tạo, hoàn lại lượt đã consume, cập nhật capacity và ghi recovery audit | Operations Lead và Device Vendor |
| OQ-006 | Capacity được kiểm soát đồng thời ở cấp chi nhánh và pool zone; request chỉ được phép khi cả hai còn chỗ | Operations Lead |
| OQ-007 | Locker là tùy chọn ở cả ba chi nhánh; một số lớp có thể yêu cầu locker theo cấu hình riêng | Operations Lead |
| OQ-008 | Nếu SRS yêu cầu tích hợp thanh toán ngoài, dự án dùng một payment provider qua adapter; lựa chọn cụ thể phải hoàn tất trong mốc thiết kế tuần 6 | Đại diện Khách hàng và Technical Lead |
| OQ-009 | Giá niêm yết đã gồm thuế; số tiền làm tròn đến 1 VND; tích hợp hóa đơn điện tử nằm ngoài phạm vi giai đoạn này | Finance Lead |
| OQ-010 | Pass chưa kích hoạt và chưa sử dụng được hoàn 100% trong 7 ngày; pass đã sử dụng không hoàn, trừ lỗi do SunSwim và cần Manager duyệt | Finance Lead và Product Owner |
| OQ-011 | Member dưới 16 tuổi bắt buộc có guardian, emergency contact và waiver còn hiệu lực trước khi tham gia lớp | Legal và Operations Lead |
| OQ-012 | Hồ sơ hội viên được giữ trong thời gian hoạt động và 24 tháng sau lần giao dịch cuối; payment và audit giữ 5 năm; hết hạn thì xóa hoặc ẩn danh theo loại dữ liệu | Legal và Security Lead |
| OQ-013 | Hệ thống chịu ít nhất 200 giao dịch đồng thời; API phản hồi không quá 1,5 giây; toàn bộ thao tác quét check-in và gán tủ tại quầy dưới 5 giây | Đại diện Chủ đầu tư và Technical Lead |
| OQ-014 | Operations quản lý lịch ngày lễ Việt Nam trong hệ thống; lịch năm kế tiếp phải được công bố trước ngày 01/12 | Operations Lead |
| OQ-015 | Lớp theo khóa bán ở cấp Offering; lớp drop-in bán theo từng Session; nghỉ có lý do không consume và Manager có thể cấp một make-up credit | Training Manager |

## 4. Kết quả chốt các câu hỏi trước UAT

| ID | Quyết định baseline | Owner xác nhận khi triển khai thật |
|---|---|---|
| OQ-016 | Member được chọn pass ưu tiên trong PWA; nếu không chọn, gate dùng pass hợp lệ có ngày hết hạn sớm nhất | Product Owner |
| OQ-017 | Early resume hoàn lại phần quota freeze chưa dùng, làm tròn theo ngày | Product Owner |
| OQ-018 | Waitlist giữ chỗ 2 giờ sau invitation; member thanh toán trong khoảng giữ chỗ | Training Manager |
| OQ-019 | Session còn mở được auto-close sau 2 giờ kể từ giờ đóng cửa; trạng thái là `RECONCILED` và occupancy được điều chỉnh | Operations Lead |
| OQ-020 | Receptionist được sửa tên, điện thoại, email, ngày sinh và emergency contact; trạng thái khóa, waiver và thông tin nhạy cảm cần Manager | Product Owner và Security Lead |
| OQ-021 | Báo cáo trực tiếp tối đa 31 ngày; export tối đa 100.000 dòng; link tải hết hạn sau 24 giờ; PII được masking nếu thiếu quyền | Finance Lead và Security Lead |
| OQ-022 | Không cho transfer, gift hoặc share pass trong phạm vi dự án | Product Owner |

## 5. Giả định lập kế hoạch

- Một pháp nhân vận hành ba chi nhánh; multi-tenant SaaS ngoài phạm vi.
- Mỗi chi nhánh có nhiều gate và pool zone.
- Member PWA không cần hoạt động offline; yêu cầu offline chỉ áp dụng cho máy quầy lễ tân phục vụ check-in.
- Gate reader gửi thông tin RFID hoặc QR cho phần mềm tại quầy. Local Cache chỉ giữ tập dữ liệu tối thiểu đã được chuẩn bị trước, không thay thế toàn bộ business rule trên máy chủ.
- SunSwim không lưu PAN đầy đủ, CVV hoặc PIN; phần nhập dữ liệu thẻ do payment provider cung cấp.
- Redis, dashboard read model và snapshot là dữ liệu dẫn xuất, có thể dựng lại.
- Dữ liệu dùng trong phát triển và báo cáo học phần là synthetic hoặc đã masking.
- Báo giá chi tiết chỉ được coi là cam kết sau khi hoàn tất lựa chọn nhà cung cấp.

## Thuật ngữ cần biết

| Thuật ngữ | Giải thích dễ hiểu |
|---|---|
| Baseline | Bộ quyết định tạm thời được chốt để nhóm có thể lập kế hoạch thống nhất. |
| Snapshot | Bản chụp dữ liệu tại một thời điểm, được giữ lại để lịch sử không đổi theo cấu hình mới. |
| Offline/Local Cache | Dữ liệu tối thiểu được lưu tại máy quầy để tiếp tục kiểm tra check-in trong thời gian mất mạng. |
| Settlement | Xác nhận đáng tin cậy rằng giao dịch thanh toán đã hoàn tất. |
| Idempotent | Có thể xử lý lại cùng yêu cầu mà không tạo thêm kết quả trùng. |
