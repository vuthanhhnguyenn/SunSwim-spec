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

## 2. Permission model

Permission là tổ hợp `action + resource + scope`, ví dụ:

```text
member.read:branch
member.update:branch
pass.adjust:branch
access.override:branch
pricing.publish:organization
refund.approve:branch
report.revenue.export:organization
```

Không hard-code role vào business logic. Role chỉ là bundle permission; backend luôn kiểm tra permission và branch scope.

## 3. Ma trận quyền rút gọn

| Domain/action | Org Admin | Manager | Reception | Coach | Finance | Member |
|---|---:|---:|---:|---:|---:|---:|
| Member R/C/U | A | B | B, field-limited | Assigned R | Limited R | Self |
| Pass product publish | A | B | R | – | R | R active |
| Pass issue/adjust/cancel | A | B/A | Issue, limited adjust | – | R | Own R |
| Access override | A | B | Conditional | – | – | – |
| Capacity limit change | A | B/A | R | – | – | R optional |
| Freeze approve | A | B/A | Conditional | – | – | Request |
| Class manage | A | B | Conditional | Assigned R/U | R | Enroll |
| Locker emergency unlock | A | Permission | Conditional | – | – | – |
| Order/payment | A | B | B | – | R | Own online |
| Refund approve | A | B/A | Request/limited | – | A | Request |
| Pricing publish | A | B | R | – | R | Quote |
| Revenue export | A | B | Conditional B | – | B/A | – |

`A`: organization-wide, `B`: branch-scoped, `A` trong action nhạy cảm: approval/override.

## 4. Separation of duties

- Refund trên ngưỡng cấu hình: requester không được tự approve.
- Manual price override trên ngưỡng: cần manager approval.
- Emergency locker unlock: strong re-authentication và audit reason.
- Sửa capacity limit khi branch đang hoạt động: manager permission và audit old/new.
- Support không được xem dữ liệu tài chính/PII ngoài ticket scope.

## 5. Device identity

- Mỗi gate có `device_id` riêng, credential xoay vòng được và gắn cứng với `branch_id/gate_id`.
- Request khai branch khác claim của device bị từ chối.
- Device bị revoke không được gọi API kể cả QR hợp lệ.
- Clock skew, nonce/request ID và rate limit được theo dõi; không tin timestamp device làm thời gian authoritative.

