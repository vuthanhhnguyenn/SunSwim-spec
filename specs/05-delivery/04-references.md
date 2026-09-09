# Tham khảo và benchmark

Các nguồn dưới đây dùng để kiểm tra cách tiếp cận, không phải tuyên bố SunSwim tương thích/chứng nhận và không phải endorsement nhà cung cấp.

## 1. Standards và architecture

| Nguồn | Cách áp dụng |
|---|---|
| [OpenAPI Specification](https://spec.openapis.org/oas/) | Machine-readable HTTP API contract; khóa minor/patch khi implementation bắt đầu |
| [RFC 9457: Problem Details for HTTP APIs](https://datatracker.ietf.org/doc/html/rfc9457) | Error envelope thống nhất thay format tự chế |
| [CloudEvents](https://cloudevents.io/) | Event envelope và metadata nhất quán |
| [OWASP ASVS](https://owasp.org/www-project-application-security-verification-standard/) | Baseline verification cho web/API; trang chính thức nêu ASVS 5.0.0 là stable |
| [PCI SSC: card verification codes](https://www.pcisecuritystandards.org/faqs/are-merchants-allowed-to-request-card-verification-codes-values-from-cardholders/) | Không lưu CVV/CVC/CID sau authorization, kể cả mã hóa |
| [Microsoft: Transactional Outbox](https://learn.microsoft.com/en-us/azure/architecture/databases/guide/transactional-out-box-cosmos) | State và event intent commit atomically; relay và consumer idempotent |
| [PostgreSQL constraints](https://www.postgresql.org/docs/current/ddl-constraints.html) | Unique/check/FK/exclusion constraints cho invariant |
| [PostgreSQL explicit locking](https://www.postgresql.org/docs/current/explicit-locking.html) | Row locking và deadlock considerations cho gate/capacity/payment |
| [PostgreSQL date/time types](https://www.postgresql.org/docs/current/datatype-datetime.html) | `timestamptz`, timezone và date semantics |

Phiên bản PostgreSQL/runtime cụ thể chưa được giả định. Khi stack được chọn, cần pin version và đối chiếu documentation đúng version trước khi viết DDL.

## 2. Aquatics/leisure product benchmark

| Nguồn | Capability quan sát được | Tác động đến SunSwim |
|---|---|---|
| [SportsEngine Motion for swimming](https://www.sportsengine.com/motion/sports/swimming/) | Registration, class management, attendance, fees, waivers, family communication | Xác nhận class/attendance/waiver/family là capability phổ biến |
| [Cohiva Complex](https://www.cohiva.com/products/complex) | Multi-location, memberships, POS, bookings, access control, family accounts, suspensions | Ủng hộ unified member/venue record và cross-domain reconciliation |
| [Swum swim school software](https://swum.ai/swim-school-software/) | Family/swimmer record, class/package, waitlist, waiver, progression, credits, multi-location | Chỉ ra gap cho kids/guardian, progression và make-up credit |
| [SwimClub Manager](https://www.swimclubmanager.co.uk/features/) | Member, attendance, finance, reporting, communication/import | Gợi ý migration/import và communication là backlog cần cân nhắc |

## 3. Nguyên tắc sử dụng benchmark

- Không sao chép UI/workflow nhà cung cấp.
- Không thêm feature chỉ vì đối thủ có; cần value, owner, release và acceptance criteria.
- Capability liên quan safety, finance, minors hoặc privacy phải qua domain/legal review.
- Marketing claim không được dùng làm bằng chứng kiến trúc/performance.

