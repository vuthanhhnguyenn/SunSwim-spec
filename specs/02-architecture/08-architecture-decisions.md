# Architecture Decision Records

Các ADR dưới đây có trạng thái `BASELINED` cho báo cáo và thiết kế. Trước M3, Architecture Board review lại và tạo change request nếu cần thay đổi.

## ADR-001: Modular monolith

- Status: Baselined.
- Decision: Một deployable application với bounded modules; worker tách process.
- Consequences: Transaction đơn giản và vận hành nhẹ; cần kiểm tra dependency để tránh “big ball of mud”.
- Revisit when: module có tải/ownership/compliance độc lập hoặc deployment coupling gây trở ngại đo được.

## ADR-002: PostgreSQL system of record

- Status: Baselined.
- Decision: Một PostgreSQL cluster/schema separation, normalized model và DB constraints.
- Consequences: Strong consistency tốt; phải quản lý connection/lock/index/migration cẩn thận.

## ADR-003: Transactional outbox

- Status: Baselined.
- Decision: State change và integration event ghi cùng transaction; relay delivery at-least-once.
- Consequences: Không mất event intent; cần inbox/idempotent consumer, retry và backlog operations.

## ADR-004: REST + OpenAPI + RFC 9457

- Status: Baselined.
- Decision: JSON REST `/api/v1`, OpenAPI machine-readable, error Problem Details.
- Consequences: Dễ tích hợp web/device; action domain đặc biệt dùng sub-resource/command endpoint có ngữ nghĩa rõ.

## ADR-005: SSE cho dashboard realtime

- Status: Baselined.
- Decision: SSE one-way + snapshot refetch; chưa dùng WebSocket.
- Consequences: Đơn giản reconnect/proxy; không phù hợp nếu sau này cần command bidirectional liên tục.

## ADR-006: Opaque/signed QR, không nhúng PII

- Status: Baselined.
- Decision: Credential resolve server-side; rotate/revoke được.
- Consequences: Cần online access ở MVP; giảm leakage và cho phép lifecycle rõ.

## ADR-007: Gate offline fail closed

- Status: `SOURCE_CONFIRMED` và được dùng trong baseline.
- Decision: Mất server/network thì gate không tự ALLOW; reception manual override.
- Consequences: An toàn hơn nhưng giảm availability tại branch; cần runbook và UX fallback.

## ADR-008: Projection cho report, không second database ở MVP

- Status: Baselined.
- Decision: Projection tables trong PostgreSQL, cập nhật từ outbox, rebuild được.
- Consequences: Ít hạ tầng; report nặng phải tách workload/index và có thể cần replica sau này.

## ADR-009: Redis không giữ correctness

- Status: Baselined.
- Decision: Redis chỉ cache/rate-limit/ephemeral coordination; source of truth vẫn PostgreSQL.
- Consequences: Redis outage degrade performance, không được gây double consume/over-capacity.
