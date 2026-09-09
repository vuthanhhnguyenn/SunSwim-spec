# Security và privacy architecture

Baseline security verification: OWASP ASVS 5.0 ở mức phù hợp rủi ro, với kiểm thử trọng tâm authentication, session, authorization, input, cryptography, API và logging. Mức ASVS cụ thể cần Security owner duyệt.

## 1. Asset và threat ưu tiên

| Asset | Threat chính | Control |
|---|---|---|
| QR credential | Copy/replay/guess | opaque random token hoặc signed short-lived token, rotation, debounce, revoke |
| Gate decision | Device giả, tamper branch, replay | per-device credential, mTLS/HMAC, request ID, nonce/skew, branch binding |
| Payment state | Fake/duplicate webhook | raw-body signature, provider lookup, amount/currency check, unique event/transaction |
| Member PII | IDOR, excessive access/export | object-level auth, branch scope, field masking, audit, least privilege |
| Admin account | Credential theft/session abuse | MFA cho privileged users, secure cookies, session revocation, re-auth sensitive action |
| Audit log | Tamper/delete/secret leakage | append-only role, restricted access, redaction, backup/integrity monitor |
| Export files | Link sharing/data exfiltration | private storage, short TTL signed link, download auth/audit, row cap/masking |

## 2. Authentication

- Admin/staff: OIDC/SSO nếu có; nếu local auth, password hashing bằng thuật toán hiện đại do framework hỗ trợ, MFA cho privileged roles.
- Member: passwordless OTP hoặc password theo product decision; chống enumeration và rate limit.
- Device: machine credential riêng, không dùng user account; rotate/revoke không downtime nếu có overlapping keys.
- Service/worker: workload identity hoặc secret manager, không commit secret vào repo.

## 3. Authorization

- Deny by default; backend enforce permission + resource ownership + branch scope.
- Query list luôn áp scope trước pagination/count.
- ID từ client không bao giờ thay thế subject trong token cho self-service endpoint.
- Action nhạy cảm yêu cầu reason, permission riêng và có thể step-up authentication.
- Test IDOR/BOLA cho mọi endpoint dùng `{id}`.

## 4. QR security

- Không encode PII/member/pass ID thô.
- Nếu opaque token dài hạn: lưu digest, cho rotate/revoke và theo dõi last used.
- Nếu signed token ngắn hạn: có audience, issuer, subject opaque, expiry, key ID và anti-replay policy.
- QR screenshot sharing không được giải quyết hoàn toàn chỉ bằng debounce; dynamic QR hoặc device-bound credential là post-MVP option.

## 5. Payment boundary

- Hosted payment/SDK của provider giữ card data ngoài SunSwim khi có thể.
- Không lưu CVV/CVC/CID/PIN hoặc sensitive authentication data sau authorization, kể cả mã hóa.
- Log/webhook storage phải redact authorization header, signature secret và card/payment token nhạy cảm.
- Refund/settlement endpoint có idempotency, approval và audit.

## 6. Privacy

- Thu thập tối thiểu; DOB/guardian/emergency contact chỉ khi có mục đích rõ.
- Consent/waiver và guardian relationship cần version, accepted_at, signer và document hash nếu đưa vào scope.
- Data export/delete request cần identity verification và legal retention exceptions.
- Non-production không dùng raw production PII; dùng masked/synthetic data.
- Backup, object storage và database mã hóa at rest; TLS in transit.

## 7. Audit events bắt buộc

- Role/scope/user changes, login security events.
- Price/product publish, manual discount.
- Pass issue/cancel/adjust/expiry override/freeze decision.
- Access override/reconciliation/capacity limit change.
- Refund/void/complimentary order/payment manual confirmation.
- Locker emergency unlock/maintenance override.
- PII export/view nhạy cảm và report export.

Audit không ghi password, OTP, raw secret, full token, CVV hoặc attachment nội dung nhạy cảm.

## 8. Security acceptance gate

- Threat model được cập nhật cho provider/device cụ thể.
- Dependency/container/IaC/secret scans pass theo severity policy.
- API authorization tests và webhook negative tests tự động.
- Pen test trước production cho access/payment/admin scope.
- Incident runbook và key rotation drill đã diễn tập.

