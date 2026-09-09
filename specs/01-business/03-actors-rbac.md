# Actors, RBAC và phân tách nhiệm vụ

## 1. Actors

| Actor | Trách nhiệm | Scope mặc định |
|---|---|---|
| Organization Admin | User/role, cấu hình toàn chuỗi | Tất cả branch |
| Pool Manager | Vận hành, pricing, approval, report | Branch được gán |
| Receptionist | Member, POS, manual access, locker | Branch trong ca làm |
| Coach | Schedule, roster, attendance | Session được gán |
| Finance | Settlement, refund, revenue export | Branch được gán hoặc toàn chuỗi |
| Member | Hồ sơ hạn chế, pass, order, freeze, enrollment | Dữ liệu bản thân/dependent được phép |
| Guest | Mua/được cấp dịch vụ hạn chế | Order/credential cụ thể |
| Gate Reader | Gửi access request | Một gate/branch cố định |
| Payment Provider | Webhook trạng thái giao dịch | Provider credential cụ thể |
| Scheduler/Worker | Expire/resume/reconcile/project | Service account tối thiểu |

## 2. Mô hình permission

Mỗi permission gồm `action + resource + scope`. Ví dụ:

```text
member.read:branch
member.update:branch
pass.adjust:branch
access.override:branch
pricing.publish:organization
refund.approve:branch
report.revenue.export:organization
```

Business logic không được hard-code role. Role chỉ gom các permission; backend vẫn phải kiểm tra permission và branch scope cho từng request.

## 3. Ma trận quyền rút gọn

| Domain/action | Org Admin | Manager | Reception | Coach | Finance | Member |
|---|---:|---:|---:|---:|---:|---:|
| Member R/C/U | A | B | B, field-limited | Assigned R | Limited R | Self |
| Pass product publish | A | B | R | Không | R | R active |
| Pass issue/adjust/cancel | A | B/A | Issue, limited adjust | Không | R | Own R |
| Access override | A | B | Conditional | Không | Không | Không |
| Capacity limit change | A | B/A | R | Không | Không | R optional |
| Freeze approve | A | B/A | Conditional | Không | Không | Request |
| Class manage | A | B | Conditional | Assigned R/U | R | Enroll |
| Locker emergency unlock | A | Permission | Conditional | Không | Không | Không |
| Order/payment | A | B | B | Không | R | Own online |
| Refund approve | A | B/A | Request/limited | Không | A | Request |
| Pricing publish | A | B | R | Không | R | Quote |
| Revenue export | A | B | Conditional B | Không | B/A | Không |

`A`: organization-wide, `B`: branch-scoped, `A` trong action nhạy cảm: approval/override.

## 4. Phân tách trách nhiệm

- Người tạo refund vượt ngưỡng cấu hình không được tự phê duyệt yêu cầu đó.
- Manual price override vượt ngưỡng cần Manager phê duyệt.
- Emergency locker unlock cần xác thực lại ở mức mạnh và phải ghi lý do vào audit.
- Khi branch đang hoạt động, chỉ người có Manager permission mới được sửa capacity limit. Audit phải lưu cả giá trị cũ và mới.
- Nhân viên Support không được xem dữ liệu tài chính hoặc PII ngoài phạm vi ticket.

## 5. Device identity

- Mỗi gate có một `device_id` và credential riêng. Credential có thể xoay vòng và được gắn với `branch_id/gate_id`.
- Hệ thống từ chối request nếu branch khai báo khác với claim của device.
- Device đã bị revoke không được gọi API, kể cả khi QR hợp lệ.
- Hệ thống theo dõi clock skew, nonce/request ID và rate limit. Timestamp từ device không phải thời gian authoritative.

## Thuật ngữ cần biết

| Thuật ngữ | Giải thích dễ hiểu |
|---|---|
| Actor | Người, thiết bị hoặc dịch vụ thực hiện hành động với hệ thống. |
| RBAC | Cách phân quyền dựa trên vai trò của người dùng. |
| Scope | Phạm vi dữ liệu hoặc chi nhánh mà một quyền được áp dụng. |
| Separation of duties | Tách các bước nhạy cảm cho nhiều người để một người không tự làm và tự duyệt. |
| Step-up authentication | Yêu cầu người dùng xác thực lại mạnh hơn trước một thao tác nhạy cảm. |
