# Danh mục lỗi

## 1. Quy tắc

- `code` phải ổn định để client xử lý; localized `detail` có thể thay đổi.
- Khi gate từ chối vì business rule, API trả decision và reason trong HTTP 200. Cùng một code ở admin eligibility API có thể trả 422.
- `retryable` chỉ cho biết client có thể technical retry, không có nghĩa user được quyền override.
- Client phải có fallback cho error code chưa biết.

## 2. Common/platform

| Code | HTTP | Retryable | Message mặc định |
|---|---:|---:|---|
| `VALIDATION_ERROR` | 422 | No | Dữ liệu chưa hợp lệ. |
| `UNAUTHORIZED` | 401 | No | Vui lòng đăng nhập lại. |
| `FORBIDDEN` | 403 | No | Bạn không có quyền thực hiện thao tác này. |
| `NOT_FOUND` | 404 | No | Không tìm thấy dữ liệu yêu cầu. |
| `VERSION_CONFLICT` | 409 | Conditional | Dữ liệu đã thay đổi. Vui lòng tải lại. |
| `IDEMPOTENCY_KEY_REUSED` | 409 | No | Mã yêu cầu đã được dùng cho nội dung khác. |
| `REQUEST_IN_PROGRESS` | 409 | Yes | Yêu cầu đang được xử lý. |
| `RATE_LIMITED` | 429 | Yes | Có quá nhiều yêu cầu. Vui lòng thử lại sau. |
| `DEPENDENCY_UNAVAILABLE` | 503 | Yes | Dịch vụ liên quan tạm thời không khả dụng. |
| `INTERNAL_ERROR` | 500 | Conditional | Hệ thống gặp lỗi. Vui lòng thử lại. |

## 3. Member/pass/freeze

| Code | HTTP | Message mặc định |
|---|---:|---|
| `MEMBER_PHONE_EXISTS` | 409 | Số điện thoại đã gắn với hồ sơ khác. |
| `MEMBER_BLOCKED` | 422 | Hồ sơ hội viên đang bị khóa. |
| `PRODUCT_NOT_FOR_SALE` | 422 | Sản phẩm hiện không mở bán. |
| `PASS_PENDING_ACTIVATION` | 422 | Vé chưa đến điều kiện kích hoạt. |
| `PASS_EXPIRED` | 422 | Vé đã hết hạn. |
| `PASS_SUSPENDED` | 422 | Vé đang được bảo lưu. |
| `PASS_CONSUMED` | 422 | Vé đã dùng hết. |
| `NO_REMAINING_ENTRY` | 422 | Vé đã hết lượt sử dụng. |
| `FREQUENCY_LIMIT_REACHED` | 422 | Bạn đã đạt giới hạn sử dụng trong kỳ. |
| `TIME_RESTRICTION` | 422 | Vé không áp dụng trong khung giờ hiện tại. |
| `BRANCH_RESTRICTION` | 422 | Vé không áp dụng tại cơ sở này. |
| `FREEZE_NOT_ALLOWED` | 422 | Vé không hỗ trợ bảo lưu. |
| `FREEZE_DATE_INVALID` | 422 | Khoảng ngày bảo lưu không hợp lệ. |
| `FREEZE_NOTICE_REQUIRED` | 422 | Yêu cầu chưa đáp ứng thời gian báo trước. |
| `FREEZE_TOO_SHORT` | 422 | Thời gian bảo lưu ngắn hơn mức tối thiểu. |
| `FREEZE_TOO_LONG` | 422 | Thời gian bảo lưu vượt mức cho phép. |
| `FREEZE_QUOTA_EXCEEDED` | 422 | Quyền bảo lưu còn lại không đủ. |
| `FREEZE_OVERLAP` | 409 | Khoảng bảo lưu bị trùng với yêu cầu khác. |
| `EVIDENCE_REQUIRED` | 422 | Cần bổ sung minh chứng. |
| `PASS_NOT_FREEZABLE` | 422 | Trạng thái vé hiện tại không cho phép bảo lưu. |
| `REQUEST_ALREADY_DECIDED` | 409 | Yêu cầu đã được xử lý. |

## 4. Access/facility

| Code | HTTP/device decision | Message mặc định |
|---|---|---|
| `INVALID_CREDENTIAL` | 200 DENY / 422 | Mã QR không hợp lệ hoặc đã hết hiệu lực. |
| `DEVICE_UNAUTHORIZED` | 401/403 | Thiết bị không được phép truy cập. |
| `DEVICE_BRANCH_MISMATCH` | 403 | Thiết bị không thuộc cơ sở yêu cầu. |
| `DUPLICATE_SCAN` | 200 DENY | Mã vừa được quét. Vui lòng chờ. |
| `NO_ELIGIBLE_PASS` | 200 DENY / 422 | Không có vé phù hợp để vào bể. |
| `ALREADY_CHECKED_IN` | 200 DENY / 409 | Hội viên hiện đã ở trong khu vực bể. |
| `NOT_CHECKED_IN` | 200 DENY / 409 | Không tìm thấy phiên vào đang mở. |
| `CAPACITY_FULL` | 200 DENY / 422 | Bể hiện đã đạt sức chứa tối đa. |
| `BRANCH_CLOSED` | 200 DENY / 422 | Cơ sở hiện đang đóng cửa. |
| `GATE_UNAVAILABLE` | 503 | Cổng tạm thời không khả dụng. |
| `LOCKER_UNAVAILABLE` | 422 | Hiện không còn tủ phù hợp. |
| `LOCKER_NOT_IN_SERVICE` | 422 | Tủ hiện không hoạt động. |
| `LOCKER_ALREADY_ASSIGNED` | 409 | Tủ đã được cấp cho phiên khác. |
| `LOCKER_BRANCH_MISMATCH` | 422 | Tủ không thuộc cơ sở của phiên. |
| `ACCESS_SESSION_REQUIRED` | 422 | Cần một phiên vào bể hợp lệ để cấp tủ tạm. |
| `ASSIGNMENT_ALREADY_RELEASED` | 409 | Lượt cấp tủ đã được kết thúc. |
| `DEVICE_COMMAND_FAILED` | 503 | Không thể điều khiển thiết bị tủ. |

## 5. Training

| Code | HTTP | Message mặc định |
|---|---:|---|
| `CLASS_FULL` | 422 | Lớp học đã đủ chỗ. |
| `ENROLLMENT_CLOSED` | 422 | Thời gian đăng ký đã kết thúc. |
| `AGE_RESTRICTION` | 422 | Học viên không phù hợp độ tuổi của lớp. |
| `LEVEL_RESTRICTION` | 422 | Học viên chưa đáp ứng trình độ yêu cầu. |
| `WAIVER_REQUIRED` | 422 | Cần hoàn tất xác nhận/waiver trước khi đăng ký. |
| `COACH_CONFLICT` | 409 | Huấn luyện viên đã có lịch trùng. |
| `STUDENT_CONFLICT` | 409 | Học viên đã có lịch trùng. |
| `SESSION_LOCKED` | 409 | Buổi học đã khóa dữ liệu điểm danh. |
| `SEAT_RESERVATION_EXPIRED` | 409 | Thời gian giữ chỗ đã hết. |
| `ALREADY_ENROLLED` | 409 | Học viên đã đăng ký lớp này. |

## 6. Commerce/pricing/reporting

| Code | HTTP | Retryable | Message mặc định |
|---|---:|---:|---|
| `QUOTE_EXPIRED` | 409 | No | Báo giá đã hết hiệu lực. |
| `QUOTE_MISMATCH` | 409 | No | Báo giá không còn khớp với yêu cầu. |
| `PRICING_NOT_RESOLVABLE` | 422 | No | Không thể xác định giá cho lựa chọn này. |
| `SLOT_OVERLAP` | 409 | No | Khung giờ bị trùng với cấu hình hiện tại. |
| `RULE_SCOPE_INVALID` | 422 | No | Phạm vi áp dụng của quy tắc không hợp lệ. |
| `RULE_PERIOD_INVALID` | 422 | No | Thời gian hiệu lực của quy tắc không hợp lệ. |
| `RULE_CONFLICT` | 409 | No | Quy tắc giá xung đột với cấu hình hiện tại. |
| `HOLIDAY_RESTRICTION` | 422 | No | Vé không áp dụng trong ngày đặc biệt này. |
| `ORDER_NOT_PAYABLE` | 409 | No | Đơn hàng không ở trạng thái có thể thanh toán. |
| `PAYMENT_AMOUNT_MISMATCH` | 409 | No | Số tiền thanh toán không khớp đơn hàng. |
| `PAYMENT_SIGNATURE_INVALID` | 401/400 | No | Không thể xác minh thông báo thanh toán. |
| `PAYMENT_DUPLICATE` | 200/409 | No | Giao dịch đã được ghi nhận. |
| `PAYMENT_NOT_SETTLED` | 409 | Conditional | Thanh toán chưa được xác nhận. |
| `PAYMENT_FAILED` | 422 | Conditional | Thanh toán không thành công. |
| `INSUFFICIENT_TENDERED_AMOUNT` | 422 | No | Số tiền nhận chưa đủ. |
| `OVERPAYMENT_NOT_ALLOWED` | 422 | No | Số tiền thanh toán vượt tổng đơn hàng. |
| `REFUND_NOT_ALLOWED` | 422 | No | Giao dịch không đủ điều kiện hoàn tiền. |
| `REFUND_LIMIT_EXCEEDED` | 422 | No | Số tiền hoàn vượt số tiền còn có thể hoàn. |
| `FULFILLMENT_PENDING` | 202/409 | Yes | Đã nhận thanh toán và đang cấp dịch vụ. |
| `REPORT_RANGE_TOO_LARGE` | 422 | No | Khoảng thời gian quá lớn cho báo cáo trực tiếp. |
| `REPORT_FILTER_INVALID` | 422 | No | Bộ lọc báo cáo không hợp lệ. |
| `REPORT_NOT_READY` | 202/409 | Yes | Báo cáo đang được xử lý. |
| `EXPORT_LIMIT_EXCEEDED` | 422 | No | Dữ liệu xuất vượt giới hạn. |
| `EXPORT_EXPIRED` | 410 | No | Tệp xuất đã hết hạn. |
| `DATA_SCOPE_FORBIDDEN` | 403 | No | Bạn không có quyền xem dữ liệu trong phạm vi này. |
| `PROJECTION_STALE` | 503 | Yes | Dữ liệu báo cáo đang chậm cập nhật. |

## 7. Logging rule

Error log phải có code, requestId, correlationId và safe metadata. Log không được chứa full QR token, signature, payment secret, OTP, password, CVV hoặc toàn bộ attachment payload.

## Thuật ngữ cần biết

| Thuật ngữ | Giải thích dễ hiểu |
|---|---|
| Error code | Mã ổn định để client biết loại lỗi và chọn cách xử lý. |
| HTTP status | Mã số cho biết kết quả xử lý request ở mức giao thức. |
| Retryable | Cho biết request có thể thử lại sau một lỗi kỹ thuật tạm thời. |
| Fallback | Cách xử lý thay thế khi client gặp code hoặc tình huống chưa biết. |
| Metadata | Thông tin mô tả thêm cho lỗi, không phải dữ liệu nghiệp vụ chính. |
