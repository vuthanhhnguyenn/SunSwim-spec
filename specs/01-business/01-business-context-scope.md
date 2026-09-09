# Bối cảnh kinh doanh và phạm vi

## 1. Vấn đề cần giải quyết

SunSwim cần một nguồn dữ liệu vận hành thống nhất cho bán hàng, entitlement, ra/vào, sức chứa và lớp học. Quy trình rời rạc làm tăng các lỗi có chi phí cao: thu tiền nhưng không cấp quyền, dùng vé hai lần, bể vượt sức chứa, bảo lưu không chặn gate, locker cấp trùng và báo cáo không đối soát được.

## 2. Mục tiêu kinh doanh

| ID | Mục tiêu | Chỉ số gợi ý |
|---|---|---|
| OBJ-COM-001 | Mọi khoản thu gắn được với order, payment và fulfillment | 100% payment settled truy vết được |
| OBJ-ENT-001 | Quyền sử dụng pass được kiểm tra nhất quán | 0 pass âm lượt; 0 fulfillment trùng |
| OBJ-ACCESS-001 | Vào/ra nhanh, an toàn và audit được | Gate decision P95 theo NFR; 0 over-capacity do race |
| OBJ-OPS-001 | Nhân viên thấy occupancy/locker gần realtime | Độ trễ dashboard theo SLO |
| OBJ-TRAIN-001 | Vận hành lớp, coach, enrollment, attendance thống nhất | 0 double-booking đã xác nhận |
| OBJ-REPORT-001 | Số liệu báo cáo có định nghĩa và drill-down | Chênh lệch settlement/report trong tolerance |

## 3. In scope

- 1–3 branch trong một tổ chức.
- Member/guest, 3 loại pass, lifecycle và usage ledger.
- POS cash trong R1; online/QR payment integration trong R2.
- QR gate check-in/out, manual override, live capacity.
- Freeze, locker temporary/fixed, dynamic pricing.
- Class/course/session, coach, enrollment, waitlist, attendance.
- Revenue, traffic, occupancy, export có quyền.
- Admin Web, Member responsive Web/PWA, Device API.
- RBAC theo branch, audit, notifications nền tảng.

## 4. Out of scope mặc định

- Multi-tenant SaaS cho nhiều công ty độc lập.
- Payroll/commission coach, general ledger, full accounting.
- Biometric access, face recognition, native mobile app.
- Gate offline entitlement cache trong MVP.
- Inventory/retail stock management đầy đủ.
- Lane reservation, facility booking và competition/meet management.
- Marketing automation/CRM lead pipeline.
- Lưu dữ liệu thẻ nhạy cảm hoặc tự xử lý card acquiring.

Guardian, waiver và make-up credit đã có policy tối thiểu trong baseline. Household portal đầy đủ, recurring billing và swimmer progression vẫn thuộc deferred backlog; xem `05-delivery/03-risk-register.md` và decision baseline.

## 5. Value streams

```mermaid
flowchart LR
    A["Discover product"] --> B["Price quote"] --> C["Order"] --> D["Payment"] --> E["Fulfillment"] --> F["Use entitlement"]
    F --> G["Access/attendance"] --> H["Operational data"] --> I["Report & reconcile"]
```

## 6. Business invariants

1. Không có entitlement trả phí trước settlement, trừ free/complimentary override được audit.
2. Một request idempotent không tạo tác động nghiệp vụ lần hai.
3. Remaining entries không âm; usage chỉ phát sinh khi access thành công theo policy.
4. Occupancy không vượt limit nếu không có override hợp lệ.
5. Lịch sử giá, payment, usage, access và audit không bị sửa để “khớp số”.
6. Dữ liệu branch bị giới hạn theo phạm vi quyền ở backend.
7. Cache không quyết định sự thật của payment, pass usage hoặc capacity.

## 7. Benchmark gap có chủ đích

Các sản phẩm quản lý aquatic/sports hiện có thường kết nối membership, POS, class, booking và access; nhóm swim-school còn nhấn mạnh family/guardian, waiver, progression, make-up credits và recurring billing. SunSwim MVP tập trung sâu hơn vào access/capacity/pass, nhưng cần Business quyết định sớm family/guardian và waiver nếu phục vụ lớp trẻ em. Nguồn benchmark được ghi tại `05-delivery/04-references.md`.
