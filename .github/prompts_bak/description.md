# MÔ TẢ DỰ ÁN: HỆ THỐNG TƯỜNG LỬA ỨNG DỤNG WEB (WAF)

## 1. Mục Tiêu Dự Án
Dự án xây dựng hệ thống Web Application Firewall (WAF) nhằm bảo vệ toàn diện cho các ứng dụng web trước các mối đe dọa hiện đại. Hệ thống hướng tới sự dễ dàng triển khai, quản lý, mở rộng, đồng thời tích hợp sâu các công nghệ bảo mật tiên tiến như ModSecurity, Nginx/Apache và cung cấp giao diện quản trị tập trung, thân thiện.

## 2. Kiến Trúc Hệ Thống
| Thành phần         | Công nghệ Đề xuất                | Chức năng chính |
|--------------------|----------------------------------|-----------------|
| WAF Engine         | Nginx hoặc Apache                | Xử lý traffic, Reverse Proxy, Caching, Load Balancing, ModSecurity |
| WAF Core Logic     | ModSecurity 3.x                  | Thực thi các quy tắc bảo mật (CRS) |
| Portal Quản trị    | Node.js/Python (Express/NestJS)  | Giao diện quản lý cấu hình, người dùng, log, giám sát |
| Cơ sở dữ liệu      | PostgreSQL                       | Lưu trữ cấu hình, domain, bảo mật, người dùng |
| Triển khai         | Docker & Docker Compose          | Dễ dàng triển khai, mở rộng, quản lý Slave Nodes |

## 3. Tính Năng Nổi Bật

### 3.1. Giao Diện Portal Quản Trị
- Chạy trên port riêng (ví dụ: 8088), bảo mật bằng path bí mật khi đăng nhập.
- Quản lý tài khoản, phân quyền theo domain, tổ chức/phòng ban, admin toàn quyền.
- Giới hạn IP đăng nhập hệ thống quản trị.

### 3.2. Quản Lý Domain & Vhost
- Lưu trữ domain, cấu hình backend, trạng thái kích hoạt bằng PostgreSQL.
- Thêm, sửa, xóa domain dễ dàng qua giao diện.
- Chỉ cho phép cấu hình vhost dạng Reverse Proxy tới backend (HTTP/HTTPS).

### 3.3. Web Server & ModSecurity
- Tùy chọn cài đặt Nginx hoặc Apache (không cài sẵn, người dùng lựa chọn khi cài đặt).
- Tùy chọn cài đặt ModSecurity phù hợp với web server đã chọn.
- Kích hoạt CRS rule có chọn lọc cho các lỗ hổng: SQL Injection, XSS, RCE, LFI, SESSION-FIXATION, PHP, PROTOCOL-ATTACK, DATA-LEAKAGES, SSRF, PHP-Variables, Web Shell.

### 3.4. Quản Lý Bảo Mật & Truy Cập
- Quản lý whitelist/blacklist theo IP, GeoIP, User-Agent, URL, Method, Header, Cookie, Body, Size Request, Size Response, Referrer, Protocol.
- Anti-Bot Challenge: CAPTCHA, JavaScript challenge, block, xác thực khác cho các đối tượng đã được whitelist/blacklist.
- Rate limit, connection limit, bandwidth limit.

### 3.5. Quản Lý SSL & Caching
- Cấu hình SSL tự động (Let's Encrypt) hoặc thủ công cho vhost và portal.
- Cache tĩnh (static file) với các tùy chọn nâng cao: TTL, cache bypass theo cookie/header.

### 3.6. Load Balancing & Health Check
- Load balancing với các thuật toán: round-robin, least connections, ip-hash.
- Health check backend server, tự động loại bỏ server lỗi khỏi pool.

### 3.7. Log, Giám Sát & Cảnh Báo
- Xem log web server (Nginx/Apache) và log thao tác hệ thống của các tài khoản.
- Ship log qua ELK Stack hoặc các hệ thống SIEM khác.
- Giám sát hiệu suất hệ thống (CPU, RAM, Disk I/O, kết nối, tốc độ phản hồi).
- Cảnh báo hiệu suất, sự cố bảo mật qua Telegram (ưu tiên) và Email.

### 3.8. Backup & Restore
- Backup toàn bộ cấu hình hệ thống và cơ sở dữ liệu.
- Khôi phục cấu hình từ các bản backup.

### 3.9. Triển Khai & Mở Rộng
- Thiết kế hoàn toàn tương thích với Docker và Docker Compose.
- Hỗ trợ Slave Nodes để mở rộng quy mô, tăng hiệu suất, đồng bộ cấu hình tự động từ Master Node.

## 4. Giá Trị Mang Lại
- Bảo vệ ứng dụng web trước các mối nguy hại hiện đại.
- Dễ dàng triển khai, quản lý, mở rộng cho doanh nghiệp/tổ chức.
- Giao diện quản trị tập trung, thân thiện, bảo mật cao.
- Tích hợp sâu các công nghệ bảo mật tiên tiến, linh hoạt tùy biến theo nhu cầu thực tế.

## 5. Kết Luận
Dự án WAF là giải pháp bảo mật toàn diện, hiện đại, dễ triển khai và mở rộng, phù hợp cho mọi tổ chức/doanh nghiệp có nhu cầu bảo vệ ứng dụng web trước các mối nguy hại ngày càng tinh vi.

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