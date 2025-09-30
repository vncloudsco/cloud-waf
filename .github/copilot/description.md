Mô Tả Dự Án: Hệ Thống Tường Lửa Ứng Dụng Web (WAF) Tích Hợp
1. Mục Tiêu Dự Án
Phát triển một giải pháp Web Application Firewall (WAF) mạnh mẽ, dễ dàng triển khai (sử dụng Docker), và có khả năng mở rộng, cung cấp khả năng bảo vệ toàn diện cho các ứng dụng web bằng cách tích hợp sâu với ModSecurity và Nginx/Apache cùng với một Portal Quản trị tập trung.

2. Kiến Trúc Hệ Thống Đề Xuất
| Thành phần | Công nghệ Đề xuất | Mục đích |
| WAF Engine | Nginx (Mặc định) hoặc Apache | Xử lý traffic, Reverse Proxy, Caching, Load Balancing, ModSecurity. |
| WAF Core Logic | ModSecurity 3.x (Connector cho Nginx/Apache) | Thực thi các quy tắc bảo mật (CRS). |
| Portal Quản trị | Node.js (dùng framework Express/NestJS) | Giao diện người dùng (UI) quản lý cấu hình, người dùng, log và giám sát. |
| Cơ sở dữ liệu | PostgreSQL | Lưu trữ cấu hình hệ thống, dữ liệu vhost, tùy chọn bảo mật, và dữ liệu người dùng. |
| Triển khai | Docker và Docker Compose | Đảm bảo tính nhất quán và dễ dàng mở rộng (Slave Nodes). |

3. Các Tính Năng Chi Tiết của Hệ Thống
A. Cấu Trúc Quản Trị và Giao Diện Portal
Portal Quản trị (Portal Administration):

Nền tảng: Xây dựng bằng Node.js/Express chạy trên một cổng riêng biệt (ví dụ: 8088).

Giao diện: Cung cấp giao diện trực quan để quản lý toàn bộ hệ thống.

Bảo mật Truy cập: Link đăng nhập Portal yêu cầu kèm theo một Path bí mật (ví dụ: /login/a9b3c5d7) để tăng cường bảo mật truy cập.

Quản lý Tài khoản và Phân quyền:

Admin Toàn quyền: Có quyền truy cập và quản lý mọi khía cạnh của hệ thống và tất cả các domain.

Quản lý Tổ chức/Phòng ban (Organization/Department): Phân quyền người dùng theo nhóm, giới hạn phạm vi quản lý.

Phân quyền theo Domain: Cho phép người dùng hoặc nhóm chỉ quản lý các vhost/domain cụ thể được chỉ định.

Hạn chế Đăng nhập: Cho phép giới hạn số lượng IP hoặc dải IP được phép truy cập vào Portal Quản trị (cổng 8088).

B. Cấu hình Vhost và Reverse Proxy
Hỗ trợ Web Server Tùy chọn:

Hệ thống cho phép người dùng tùy chọn và cài đặt Nginx hoặc Apache làm Web Server/WAF Engine.

Cấu hình WAF Engine được triển khai tự động dựa trên lựa chọn này.

Cấu hình Reverse Proxy Bắt buộc:

Chỉ cho phép cấu hình vhost (Virtual Host) với tùy chọn là Reverse Proxy tới một Backend Server khác.

Backend có thể là giao thức HTTP hoặc HTTPS (đảm bảo hỗ trợ SNI nếu cần).

Portal quản trị tự động tạo và phân phối các file cấu hình vhost phù hợp cho Nginx/Apache.

Quản lý Domain:

Sử dụng PostgreSQL để lưu trữ các tùy chọn liên quan tới domain (thêm, sửa, xóa, trạng thái kích hoạt, cấu hình backend, v.v.).

C. Bảo mật WAF (ModSecurity và Quy tắc)
Tùy chọn Cài đặt ModSecurity: Cho phép người dùng tùy chọn cài đặt ModSecurity phù hợp với Web Server đã chọn (ví dụ: ngx_http_modsecurity_module cho Nginx hoặc mod_security2 cho Apache).

Kích hoạt CRS (Core Rule Set) có chọn lọc:

Người dùng có thể kích hoạt các nhóm quy tắc CRS cụ thể của ModSecurity để chống lại các lỗ hổng:

SQL Injection (SQLi)

Cross-Site Scripting (XSS)

Remote Code Execution (RCE)

Local File Inclusion (LFI)

Session Fixation (SESSION-FIXATION)

PHP Attacks (PHP, PHP-VARIABLES, Web Shell)

Protocol Attacks (PROTOCOL-ATTACK)

Data Leakages (DATA-LEAKAGES)

Server-Side Request Forgery (SSRF)

Bộ Quy tắc Giới hạn (Limitation Rules):

Rate Limit: Giới hạn số lượng yêu cầu (requests) trên mỗi đơn vị thời gian (ví dụ: 10 req/s/IP).

Connection Limit: Giới hạn số lượng kết nối đồng thời từ một IP.

Bandwidth Limit: Giới hạn băng thông sử dụng cho mỗi kết nối/người dùng.

Bộ Quy tắc Whitelist/Blacklist (W/B):

Hệ thống cho phép quản trị viên cấu hình W/B dựa trên nhiều tiêu chí:

IP / GeoIP

User-Agent

URL

HTTP Method

Header / Cookie

Request Body / Request Size / Response Size

Referrer / Protocol

Anti-Bot Challenge:

Sau khi W/B được cấu hình, WAF có thể kích hoạt cơ chế Anti-Bot Challenge (CAPTCHA, JavaScript challenge, hoặc Blocking) cho các yêu cầu không nằm trong danh sách White/Black list hoặc nghi ngờ là bot.

D. SSL/TLS và Caching
Quản lý SSL:

SSL Tự động: Tích hợp tính năng cấp chứng chỉ SSL tự động (ví dụ: thông qua Let's Encrypt).

SSL Thủ công: Cho phép người dùng tải lên các chứng chỉ SSL đã có (Certificate Key Pair) cho từng vhost và cho Portal Quản trị.

Cache Tĩnh (Static File Caching):

Cho phép kích hoạt caching cho các loại tệp tĩnh (CSS, JS, images, v.v.).

Cấu hình Nâng cao: Cung cấp các tùy chọn cấu hình như thời gian sống (TTL), bỏ qua cache (Cache Bypass) dựa trên cookie hoặc header.

E. Load Balancing và Health Check
Tính năng Load Balancing: Cung cấp khả năng phân tải traffic tới nhiều Backend Server.

Thuật toán Hỗ trợ:

Round-Robin (Luân phiên)

Least Connections (Kết nối ít nhất)

IP Hash (Dựa trên địa chỉ IP nguồn)

Health Check Backend Server:

Liên tục kiểm tra trạng thái sức khỏe (Health Check) của các Backend Server trong Pool.

Tự động loại bỏ các server bị lỗi khỏi Pool và đưa vào lại khi chúng hoạt động trở lại.

F. Log, Giám sát và Cảnh báo
Xem Log Tập trung:

Log Web Server: Xem log truy cập và log lỗi của Web Server đã chọn (Nginx/Apache) theo từng domain.

Log Thao tác Hệ thống: Xem lịch sử thao tác chi tiết của mọi loại tài khoản (Admin, Tổ chức, Domain) trên Portal Quản trị.

Hỗ trợ Ship Log: Có khả năng chuyển tiếp (ship) toàn bộ log WAF và log thao tác hệ thống tới nền tảng tập trung như ELK Stack (Elasticsearch, Logstash, Kibana) hoặc các hệ thống SIEM khác.

Giám sát Hiệu suất:

Liên tục giám sát hiệu suất hệ thống (CPU, RAM, Disk I/O, số lượng kết nối, tốc độ phản hồi).

Cảnh báo Hiệu suất: Gửi cảnh báo khi ngưỡng hiệu suất bị vi phạm hoặc hệ thống quá tải.

Cảnh báo Sự kiện Bảo mật:

Gửi cảnh báo tức thời khi có sự cố bảo mật quan trọng (ví dụ: bị tấn công SQLi, ModSecurity Rule bị kích hoạt, v.v.).

Kênh Cảnh báo Ưu tiên: Hỗ trợ gửi qua Telegram (kênh cảnh báo chính) và Email.

G. Khả năng Mở rộng và Triển khai
Thiết kế Docker Hóa Hoàn toàn:

Toàn bộ hệ thống (Portal, PostgreSQL, WAF Engine) được đóng gói trong các container Docker riêng biệt.

Sử dụng Docker Compose để dễ dàng triển khai, quản lý cấu hình và liên kết các dịch vụ.

Hỗ trợ Slave Nodes:

Hệ thống được thiết kế để mở rộng theo chiều ngang (Horizontal Scaling) bằng cách triển khai nhiều Slave Nodes.

Slave Nodes bao gồm WAF Engine và được đồng bộ hóa cấu hình tự động từ Master Node thông qua cơ sở dữ liệu PostgreSQL. Điều này giúp tăng cường hiệu suất và khả năng chịu tải.

Backup và Restore:

Cung cấp tính năng sao lưu (Backup) toàn bộ cấu hình hệ thống và cơ sở dữ liệu (PostgreSQL).

Hỗ trợ khôi phục (Restore) cấu hình hệ thống từ các bản sao lưu đã có.