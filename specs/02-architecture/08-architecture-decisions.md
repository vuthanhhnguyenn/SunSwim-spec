# Architecture Decision Records

Các ADR dưới đây đã được chốt để làm báo cáo và thiết kế. Architecture Board review lại trước M3; nếu cần sửa, nhóm tạo change request.

## ADR-001: Modular monolith

- Trạng thái: Baselined.
- Quyết định: Dùng một deployable application gồm các bounded module; worker chạy ở process riêng.
- Hệ quả: Transaction và vận hành đơn giản hơn, nhưng phải kiểm soát dependency để tránh "big ball of mud".
- Xem xét lại khi: một module có yêu cầu riêng về tải, ownership, compliance hoặc deployment coupling gây ra vấn đề đo được.

## ADR-002: PostgreSQL system of record

- Trạng thái: Baselined.
- Quyết định: Dùng một PostgreSQL cluster, tách schema, normalize model và áp dụng database constraint.
- Hệ quả: Hệ thống có strong consistency, nhưng cần quản lý connection, lock, index và migration cẩn thận.

## ADR-003: Transactional outbox

- Trạng thái: Baselined.
- Quyết định: State change và integration event được ghi trong cùng transaction; relay delivery theo cơ chế at-least-once.
- Hệ quả: Event intent không bị mất, nhưng consumer cần inbox và idempotency. Đội vận hành cũng phải xử lý retry và backlog.

## ADR-004: REST + OpenAPI + RFC 9457

- Trạng thái: Baselined.
- Quyết định: Dùng JSON REST `/api/v1`, OpenAPI machine-readable và Problem Details cho lỗi.
- Hệ quả: Web và device dễ tích hợp. Các action đặc biệt của domain dùng sub-resource hoặc command endpoint có tên rõ nghĩa.

## ADR-005: SSE cho dashboard realtime

- Trạng thái: Baselined.
- Quyết định: Dùng SSE một chiều và tải lại snapshot khi kết nối lại; chưa dùng WebSocket.
- Hệ quả: Reconnect và proxy đơn giản hơn. Giải pháp này không phù hợp nếu sau này cần gửi command hai chiều liên tục.

## ADR-006: Opaque/signed QR, không nhúng PII

- Trạng thái: Baselined.
- Quyết định: Server resolve credential; credential có thể rotate và revoke.
- Hệ quả: Thông tin khó bị lộ và credential có vòng đời rõ. Khi ngoại tuyến, Local Cache chỉ lưu dữ liệu tối thiểu cần cho quyết định tại quầy.

## ADR-007: Check-in ngoại tuyến có kiểm soát

- Trạng thái: `SOURCE_CONFIRMED` và được dùng trong baseline.
- Quyết định: Khi mất mạng, máy quầy dùng Offline/Local Cache, lưu tạm lịch sử quét và tự đồng bộ ngầm khi kết nối trở lại. Chỉ những yêu cầu có dữ liệu cục bộ còn hiệu lực và đủ điều kiện an toàn mới được xử lý tự động.
- Hệ quả: Luồng check-in ít bị gián đoạn hơn, nhưng nhóm phải bảo vệ dữ liệu cục bộ, quản lý độ mới, chống ghi trùng và có hàng đợi đối soát xung đột.

## ADR-008: Projection cho report, không second database ở MVP

- Trạng thái: Baselined.
- Quyết định: Projection table nằm trong PostgreSQL, được cập nhật từ outbox và có thể rebuild.
- Hệ quả: Hệ thống cần ít hạ tầng hơn. Với report nặng, đội dự án phải tách workload, bổ sung index và có thể dùng replica sau này.

## ADR-009: Redis không giữ correctness

- Trạng thái: Baselined.
- Quyết định: Redis chỉ dùng cho cache, rate limit và ephemeral coordination; PostgreSQL vẫn là source of truth.
- Hệ quả: Redis outage có thể làm giảm hiệu năng nhưng không được gây double consume hoặc over-capacity.

## Thuật ngữ cần biết

| Thuật ngữ | Giải thích dễ hiểu |
|---|---|
| ADR | Bản ghi một quyết định kiến trúc, lý do chọn và hệ quả của quyết định đó. |
| Bounded module | Module có trách nhiệm và ranh giới dữ liệu rõ ràng. |
| Projection | Bảng dữ liệu được tạo để phục vụ truy vấn hoặc báo cáo nhanh. |
| Source of truth | Nguồn dữ liệu chính thức được dùng để đưa ra quyết định. |
| Deployment coupling | Tình trạng các phần phải triển khai cùng nhau dù chỉ một phần thay đổi. |
