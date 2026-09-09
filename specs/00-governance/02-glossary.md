# Thuật ngữ và ubiquitous language

| Thuật ngữ | Định nghĩa chuẩn | Không đồng nghĩa với |
|---|---|---|
| Branch | Một cơ sở vận hành có timezone, giờ mở cửa và phạm vi quyền riêng | Pool zone |
| Pool Zone | Khu vực/bể con thuộc branch, có thể có capacity riêng | Branch |
| Member | Cá nhân có hồ sơ SunSwim; có thể chưa có pass | Account đăng nhập |
| Guest | Khách giao dịch hoặc vào bể theo vé khách, có thể không có tài khoản | Member mặc định |
| Household | Nhóm người có quan hệ người giám hộ/người phụ thuộc | Member |
| Pass Product | Cấu hình sản phẩm bán, giá cơ sở và chính sách | Quyền đã cấp |
| Member Pass | Entitlement đã cấp cho một member từ một order/override | Product |
| Pass Usage | Bút toán tiêu thụ/hoàn/điều chỉnh lượt, bất biến | Số đếm có thể sửa trực tiếp |
| Access Credential | Token QR opaque/signed dùng để resolve subject | Member ID/pass ID thô |
| Access Event | Mọi lần yêu cầu cổng, gồm allow/deny | Access Session |
| Access Session | Khoảng hiện diện từ check-in thành công đến check-out/reconciliation | Một lần quét |
| Presence | Trạng thái `OUTSIDE`/`INSIDE` tại một branch | Attendance lớp |
| Occupancy | Số access session đang mở đã được tính capacity | Tổng check-in trong ngày |
| Capacity Limit | Sức chứa cho phép của branch/zone tại thời điểm xét | Số locker |
| Freeze | Khoảng tạm ngưng quyền sử dụng pass theo policy | Cancel pass |
| Class Definition | Chương trình/lớp logic được bán hoặc công bố | Một buổi học |
| Class Session | Một buổi cụ thể có thời gian, coach, zone | Course |
| Enrollment | Quan hệ đăng ký học viên với course/class | Attendance |
| Order | Ý định mua và snapshot giá | Payment |
| Payment | Bút toán thu tiền theo phương thức/provider | Order status |
| Settlement | Xác nhận tiền thành công đủ tin cậy để fulfillment | Browser redirect |
| Refund | Giao dịch hoàn tiền tham chiếu payment/order gốc | Xóa payment |
| Fulfillment | Cấp pass, enrollment hoặc locker rental sau điều kiện thanh toán | Payment |
| Price Rule | Rule tạo discount/surcharge lúc quote | Pass access restriction |
| Reconciliation | Tác vụ có ghi vết để sửa lệch trạng thái vận hành | Sửa/xóa lịch sử trực tiếp |
| Business Day | Ngày theo timezone của branch | Ngày UTC |
| Source of Truth | Dữ liệu authoritative để ra quyết định | Cache/read model |

## Quy ước khoảng thời gian

Mọi khoảng thời gian kỹ thuật dùng dạng nửa mở `[start, end)`. Ví dụ slot 17:00–20:00 cho phép thời điểm `17:00:00`, không cho `20:00:00`. Quy ước này loại bỏ overlap tại ranh giới. Nội dung hiển thị “đến hết ngày” được chuyển thành `end_exclusive` của ngày kế tiếp theo timezone branch.

