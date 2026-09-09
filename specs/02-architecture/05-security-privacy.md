# Kiến trúc bảo mật và quyền riêng tư

Hoạt động kiểm tra bảo mật dựa trên OWASP ASVS 5.0 ở mức phù hợp với rủi ro. Kiểm thử tập trung vào authentication, session, authorization, input, cryptography, API và logging. Security owner quyết định mức ASVS cụ thể.

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

- Admin và staff dùng OIDC/SSO nếu hạ tầng có hỗ trợ. Với local auth, hệ thống hash password bằng thuật toán hiện đại do framework hỗ trợ và bắt buộc MFA cho privileged role.
- Member: passwordless OTP hoặc password theo product decision; chống enumeration và rate limit.
- Device dùng machine credential riêng, không dùng user account. Cặp key chồng lấn cho phép rotate hoặc revoke mà không gây downtime.
- Service/worker: workload identity hoặc secret manager, không commit secret vào repo.

## 3. Authorization

- Backend áp dụng deny by default và kiểm tra permission, resource ownership cùng branch scope.
- List query phải áp dụng scope trước khi pagination hoặc count.
- Với self-service endpoint, ID do client gửi không được thay thế subject trong token.
- Action nhạy cảm cần permission riêng, reason và có thể yêu cầu step-up authentication.
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

Audit không được ghi password, OTP, raw secret, full token, CVV hoặc nội dung nhạy cảm trong attachment.

## 8. Security acceptance gate

- Threat model phải được cập nhật theo provider và device đã chọn.
- Dependency/container/IaC/secret scans pass theo severity policy.
- API authorization tests và webhook negative tests tự động.
- Pen test trước production cho access/payment/admin scope.
- Incident runbook và key rotation drill đã diễn tập.

## Thuật ngữ cần biết

| Thuật ngữ | Giải thích dễ hiểu |
|---|---|
| OWASP ASVS | Bộ yêu cầu dùng để kiểm tra mức an toàn của ứng dụng web và API. |
| Threat model | Tài liệu mô tả tài sản cần bảo vệ, cách có thể bị tấn công và biện pháp kiểm soát. |
| OIDC/SSO | Cơ chế cho phép đăng nhập qua một hệ thống danh tính dùng chung. |
| IDOR/BOLA | Lỗi cho phép người dùng truy cập dữ liệu của người khác bằng cách đổi ID trong request. |
| PII | Dữ liệu có thể nhận diện một cá nhân, như tên, số điện thoại hoặc ngày sinh. |
