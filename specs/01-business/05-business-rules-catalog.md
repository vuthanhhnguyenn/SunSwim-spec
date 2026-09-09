# Danh mục business rule

Đây là danh mục business rule chuẩn. Mỗi functional spec sẽ giải thích rule tương ứng và cung cấp ví dụ chi tiết.

## Customer và entitlement

| ID | Rule chuẩn hóa | Mức |
|---|---|---|
| BR-MEM-001 | Phone đã chuẩn hóa không tạo member thứ hai mặc định; merge cần workflow riêng | MUST |
| BR-MEM-002 | Single pass có một entitlement unit và validity/session scope rõ | MUST |
| BR-MEM-003 | Subscription kiểm tra đồng thời validity, time rule và frequency limit | MUST |
| BR-MEM-004 | Remaining entries được suy ra từ usage ledger và không âm | MUST |
| BR-MEM-005 | Activation policy là `IMMEDIATE`, `FIRST_CHECK_IN` hoặc `SPECIFIC_DATE` | MUST |
| BR-MEM-006 | Duration theo ngày lịch tính ở timezone policy; end kỹ thuật là exclusive | MUST |
| BR-MEM-007 | Chỉ access commit thành công mới consume entry | MUST |
| BR-MEM-008 | Pass selection deterministic, audit được và trả về pass đã chọn | MUST |
| BR-MEM-009 | Product update không hồi tố Member Pass đã cấp, trừ migration được duyệt | MUST |
| BR-MEM-010 | Adjustment lượt là ledger entry có reason/actor, không sửa counter trực tiếp | MUST |

## Access và facility

| ID | Rule chuẩn hóa | Mức |
|---|---|---|
| BR-GATE-001 | QR không chứa PII hoặc ID nội bộ có thể suy đoán | MUST |
| BR-GATE-002 | Eligibility là conjunction của credential, member, pass, branch, time, quota, presence, capacity | MUST |
| BR-GATE-003 | Request ID và duplicate scan không tạo side effect lần hai | MUST |
| BR-GATE-004 | Presence transition chỉ `OUTSIDE→INSIDE→OUTSIDE`, exception phải là recovery/override | MUST |
| BR-GATE-005 | Occupancy được suy từ session/capacity ledger authoritative | MUST |
| BR-GATE-006 | Không ALLOW khi limit đã đạt, trừ override được bật và audit | MUST |
| BR-GATE-007 | Pass activation/usage, access session và capacity reservation commit/rollback cùng nhau | MUST |
| BR-GATE-008 | Checkout không consume pass và phải đóng session đúng branch | MUST |
| BR-GATE-009 | Server time là authoritative; device time chỉ là observation metadata | MUST |
| BR-GATE-010 | Gate offline MVP fail closed | MUST |

## Freeze

| ID | Rule chuẩn hóa | Mức |
|---|---|---|
| BR-FRZ-001 | Freeze period của cùng pass không overlap | MUST |
| BR-FRZ-002 | Freeze có hiệu lực làm eligibility deny và không consume | MUST |
| BR-FRZ-003 | Expiry extension là adjustment bằng số ngày effective, không overwrite không dấu vết | MUST |
| BR-FRZ-004 | Future freeze chỉ có hiệu lực từ `start_at` | MUST |
| BR-FRZ-005 | Resume đúng `end_exclusive`; early resume theo policy | MUST |
| BR-FRZ-006 | Approval phải revalidate quota và version để tránh concurrent approval | MUST |

## Training

| ID | Rule chuẩn hóa | Mức |
|---|---|---|
| BR-CLS-001 | Confirmed + active reservation không vượt class/session capacity | MUST |
| BR-CLS-002 | Coach không có hai session overlap | MUST |
| BR-CLS-003 | Student conflict được kiểm tra khi policy bật | SHOULD |
| BR-CLS-004 | Paid enrollment chỉ Confirmed sau settlement/complimentary approval | MUST |
| BR-CLS-005 | Attendance sau lock chỉ manager sửa và phải audit | MUST |
| BR-CLS-006 | Waitlist promotion deterministic, giữ chỗ có TTL | SHOULD |

## Locker

| ID | Rule chuẩn hóa | Mức |
|---|---|---|
| BR-LCK-001 | Một locker không có hai active assignment overlap | MUST |
| BR-LCK-002 | Temporary assignment cùng branch và gắn access session | MUST |
| BR-LCK-003 | Checkout phát lệnh release idempotent; lỗi thiết bị không xóa assignment | MUST |
| BR-LCK-004 | Maintenance/emergency locker không được assign | MUST |

## Commerce và pricing

| ID | Rule chuẩn hóa | Mức |
|---|---|---|
| BR-COM-001 | Tổng order bằng tổng line snapshot trừ discount cộng surcharge/tax; không âm | MUST |
| BR-COM-002 | Provider transaction và webhook event phải unique/idempotent | MUST |
| BR-COM-003 | Tổng settled payment bằng grand total mới chuyển order Paid, trừ credit policy | MUST |
| BR-COM-004 | Fulfillment item idempotent và chỉ sau Paid/free/approved complimentary | MUST |
| BR-COM-005 | Refund là transaction mới, không sửa payment gốc | MUST |
| BR-PRC-001 | Quote phải trả breakdown, rule IDs, currency, expiry và fingerprint/version | MUST |
| BR-PRC-002 | Rule evaluation deterministic theo priority và calculation mode | MUST |
| BR-PRC-003 | Published rule có effective range, scope và không hồi tố order | MUST |

## Reporting và platform

| ID | Rule chuẩn hóa | Mức |
|---|---|---|
| BR-RPT-001 | Net revenue = settled payments − settled refunds trong cùng reporting basis | MUST |
| BR-RPT-002 | Check-ins, unique visitors và occupancy là ba metric khác nhau | MUST |
| BR-RPT-003 | Report projection truy vết về transaction nguồn và có freshness timestamp | MUST |
| BR-PLT-001 | Mọi action nhạy cảm có immutable audit record old/new/reason/correlation | MUST |
| BR-PLT-002 | Backend kiểm tra permission và branch scope cho mọi request | MUST |
| BR-PLT-003 | Soft delete/cancel/archive thay hard delete cho dữ liệu giao dịch | MUST |

## Thuật ngữ cần biết

| Thuật ngữ | Giải thích dễ hiểu |
|---|---|
| Business rule | Quy tắc nghiệp vụ mà hệ thống phải tuân theo. |
| MUST | Yêu cầu bắt buộc phải thực hiện. |
| SHOULD | Yêu cầu nên thực hiện; chỉ bỏ khi có lý do được chấp nhận. |
| Deterministic | Cùng dữ liệu đầu vào và cùng rule thì luôn cho cùng kết quả. |
| Soft delete | Đánh dấu dữ liệu là đã hủy hoặc không còn dùng nhưng vẫn giữ lịch sử. |
