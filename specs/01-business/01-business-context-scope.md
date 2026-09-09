# Bối cảnh kinh doanh và phạm vi

## 1. Vấn đề cần giải quyết

SunSwim cần dùng chung một nguồn dữ liệu cho bán hàng, entitlement, ra vào, sức chứa và lớp học. Khi các quy trình hoạt động rời rạc, hệ thống có thể thu tiền nhưng không cấp quyền, cho dùng vé hai lần, để bể vượt sức chứa, không chặn gate trong thời gian bảo lưu, cấp trùng locker hoặc tạo báo cáo không thể đối soát.

## 2. Mục tiêu kinh doanh

| ID | Mục tiêu | Chỉ số gợi ý |
|---|---|---|
| OBJ-COM-001 | Mọi khoản thu gắn được với order, payment và fulfillment | 100% payment settled truy vết được |
| OBJ-ENT-001 | Quyền sử dụng pass được kiểm tra nhất quán | 0 pass âm lượt; 0 fulfillment trùng |
| OBJ-ACCESS-001 | Vào/ra nhanh, an toàn và audit được | Gate decision P95 theo NFR; 0 over-capacity do race |
| OBJ-OPS-001 | Nhân viên thấy occupancy/locker gần realtime | Độ trễ dashboard theo SLO |
| OBJ-TRAIN-001 | Vận hành lớp, coach, enrollment, attendance thống nhất | 0 double-booking đã xác nhận |
| OBJ-REPORT-001 | Số liệu báo cáo có định nghĩa và drill-down | Chênh lệch settlement/report trong tolerance |

## 3. Phạm vi thực hiện

- Từ 1 đến 3 branch trong một tổ chức.
- Member/guest, 3 loại pass, lifecycle và usage ledger.
- POS cash trong R1; online/QR payment integration trong R2.
- QR gate check-in/out, manual override, live capacity.
- Freeze, locker temporary/fixed, dynamic pricing.
- Class/course/session, coach, enrollment, waitlist, attendance.
- Revenue, traffic, occupancy, export có quyền.
- Admin Web, Member responsive Web/PWA, Device API.
- RBAC theo branch, audit, notifications nền tảng.

## 4. Ngoài phạm vi mặc định

- Multi-tenant SaaS cho nhiều công ty độc lập.
- Payroll/commission coach, general ledger, full accounting.
- Biometric access, face recognition, native mobile app.
- Gate offline entitlement cache trong MVP.
- Inventory/retail stock management đầy đủ.
- Lane reservation, facility booking và competition/meet management.
- Marketing automation/CRM lead pipeline.
- Lưu dữ liệu thẻ nhạy cảm hoặc tự xử lý card acquiring.

Baseline đã có policy tối thiểu cho guardian, waiver và make-up credit. Household portal đầy đủ, recurring billing và swimmer progression vẫn nằm trong deferred backlog. Chi tiết có trong `05-delivery/03-risk-register.md` và decision baseline.

## 5. Luồng tạo giá trị

```mermaid
flowchart LR
    A["Discover product"] --> B["Price quote"] --> C["Order"] --> D["Payment"] --> E["Fulfillment"] --> F["Use entitlement"]
    F --> G["Access/attendance"] --> H["Operational data"] --> I["Report & reconcile"]
```

## 6. Business invariants

1. Hệ thống không cấp entitlement trả phí trước settlement, trừ free hoặc complimentary override có audit.
2. Khi gửi lại một request idempotent, hệ thống không tạo thêm tác động nghiệp vụ.
3. Remaining entries không được âm. Usage chỉ phát sinh khi access thành công theo policy.
4. Occupancy không được vượt limit nếu không có override hợp lệ.
5. Không sửa lịch sử giá, payment, usage, access hoặc audit chỉ để "khớp số".
6. Backend phải giới hạn dữ liệu branch theo phạm vi quyền.
7. Cache không phải nguồn quyết định cho payment, pass usage hoặc capacity.

## 7. Phạm vi còn thiếu so với sản phẩm tham khảo

Các sản phẩm quản lý aquatic hoặc sports thường kết nối membership, POS, class, booking và access. Phần mềm dành cho swim school còn có family/guardian, waiver, progression, make-up credit và recurring billing. SunSwim MVP tập trung vào access, capacity và pass. Nếu cung cấp lớp trẻ em, Business cần quyết định sớm policy cho family/guardian và waiver. Danh sách nguồn tham khảo nằm trong `05-delivery/04-references.md`.

## Thuật ngữ cần biết

| Thuật ngữ | Giải thích dễ hiểu |
|---|---|
| Entitlement | Quyền sử dụng dịch vụ mà member nhận được từ pass. |
| Settlement | Trạng thái thanh toán đã được xác nhận đủ tin cậy để cấp dịch vụ. |
| Manual override | Thao tác cho phép nhân viên xử lý khác với quyết định tự động, kèm quyền và lý do. |
| Deferred backlog | Danh sách chức năng đã hoãn sang giai đoạn sau. |
| Invariant | Điều kiện luôn phải đúng, dù có nhiều giao dịch chạy cùng lúc. |
