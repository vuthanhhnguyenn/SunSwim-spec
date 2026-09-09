# Nguồn tham khảo và benchmark

Các nguồn dưới đây giúp nhóm kiểm tra cách tiếp cận. Việc đưa một nguồn vào danh sách không có nghĩa SunSwim đã tương thích, được chứng nhận hoặc endorsement nhà cung cấp đó.

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

Tài liệu chưa giả định phiên bản PostgreSQL hoặc runtime cụ thể. Sau khi chọn stack, nhóm phải pin version và đọc đúng documentation của version đó trước khi viết DDL.

## 2. Aquatics/leisure product benchmark

| Nguồn | Capability quan sát được | Tác động đến SunSwim |
|---|---|---|
| [SportsEngine Motion for swimming](https://www.sportsengine.com/motion/sports/swimming/) | Registration, class management, attendance, fees, waivers, family communication | Xác nhận class/attendance/waiver/family là capability phổ biến |
| [Cohiva Complex](https://www.cohiva.com/products/complex) | Multi-location, memberships, POS, bookings, access control, family accounts, suspensions | Ủng hộ unified member/venue record và cross-domain reconciliation |
| [Swum swim school software](https://swum.ai/swim-school-software/) | Family/swimmer record, class/package, waitlist, waiver, progression, credits, multi-location | Chỉ ra gap cho kids/guardian, progression và make-up credit |
| [SwimClub Manager](https://www.swimclubmanager.co.uk/features/) | Member, attendance, finance, reporting, communication/import | Gợi ý migration/import và communication là backlog cần cân nhắc |

## 3. Nguyên tắc sử dụng benchmark

- Không sao chép UI hoặc workflow của nhà cung cấp.
- Không thêm feature chỉ vì một sản phẩm khác có. Mỗi feature cần có value, owner, release và acceptance criteria.
- Capability liên quan đến safety, finance, minor hoặc privacy phải qua domain review và legal review.
- Không dùng marketing claim làm bằng chứng cho kiến trúc hoặc performance.

## Thuật ngữ cần biết

| Thuật ngữ | Giải thích dễ hiểu |
|---|---|
| Benchmark | Sản phẩm hoặc tài liệu được dùng làm mốc để so sánh cách tiếp cận. |
| Standard | Bộ quy tắc hoặc đặc tả được một tổ chức công bố để các hệ thống áp dụng thống nhất. |
| Endorsement | Việc một tổ chức chính thức ủng hộ hoặc xác nhận một sản phẩm. |
| Pin version | Chọn và cố định một phiên bản cụ thể để tránh thay đổi ngoài ý muốn. |
| DDL | Nhóm câu lệnh dùng để tạo hoặc thay đổi cấu trúc database. |
