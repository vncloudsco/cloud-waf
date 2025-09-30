# COPILOT INSTRUCTIONS - CLOUD WAF PROJECT

## TỔNG QUAN DỰ ÁN
Dự án xây dựng hệ thống Web Application Firewall (WAF) với kiến trúc tích hợp Nginx/Apache + ModSecurity + Portal Quản trị + PostgreSQL, được triển khai hoàn toàn bằng Docker.

## NGUYÊN TẮC PHÁT TRIỂN
- **KHÔNG** can thiệp các thành phần không cần thiết
- **KHÔNG** tạo ra tính năng không có trong mô tả dự án
- **KHÔNG** tạo ra các file test không cần thiết
- Docker sử dụng image Ubuntu 24.04 làm base image
- Ưu tiên sự đơn giản, hiệu quả và bảo mật

## KIẾN TRÚC HỆ THỐNG

### 1. Portal Quản Trị
- **Port**: 8088 (hoặc configurable)
- **Ngôn ngữ**: Node.js (Express/NestJS) - ưu tiên Node.js cho hiệu suất và ecosystem
- **Đăng nhập**: Có path bí mật (ví dụ: `/admin-secret-path-123`)
- **Giới hạn IP**: Chỉ cho phép IP được whitelist đăng nhập

### 2. Web Server Engine
- **Lựa chọn**: Nginx HOẶC Apache (không cài sẵn, người dùng chọn khi setup)
- **ModSecurity**: Cài đặt theo web server đã chọn
  - Nginx: `ngx_http_modsecurity_module`
  - Apache: `mod_security2`

### 3. Database
- **PostgreSQL**: Lưu trữ cấu hình domain, user, settings
- **Schemas**: users, domains, configurations, logs, alerts

### 4. Docker Architecture
- **Base Image**: Ubuntu 24.04
- **Multi-container**: WAF engine, Portal, PostgreSQL, ELK (optional)
- **Docker Compose**: Quản lý toàn bộ stack
- **Slave Nodes**: Hỗ trợ mở rộng horizontal

## TÍNH NĂNG CỐT LÕI

### A. Quản Lý Domain & Vhost
```
- CRUD domain qua giao diện web
- Chỉ hỗ trợ Reverse Proxy (không direct serving)
- Backend: HTTP/HTTPS configurable
- SSL: Auto (Let's Encrypt) + Manual upload
```

### B. ModSecurity CRS Rules (Có chọn lọc)
```
- SQL Injection (SQLi)
- Cross-Site Scripting (XSS)  
- Remote Code Execution (RCE)
- Local File Inclusion (LFI)
- Session Fixation
- PHP Attacks & PHP Variables
- Protocol Attacks
- Data Leakages
- SSRF (Server-Side Request Forgery)
- Web Shell Detection
```

### C. Access Control & Security
```
Whitelist/Blacklist theo:
- IP Address & CIDR
- GeoIP (Country/Region)
- User-Agent patterns
- URL paths & patterns
- HTTP Methods
- Headers (custom)
- Cookies
- Request Body patterns
- Request/Response Size
- Referrer
- Protocol (HTTP/HTTPS)
```

### D. Rate Limiting & Traffic Control
```
- Rate Limit: requests/second/IP
- Connection Limit: concurrent connections/IP
- Bandwidth Limit: KB/s per connection
- Anti-Bot Challenge: CAPTCHA, JS Challenge, Block
```

### E. Load Balancing & Health Check
```
Algorithms:
- Round Robin
- Least Connections  
- IP Hash
- Weighted Round Robin (bonus)

Health Check:
- HTTP/HTTPS endpoint monitoring
- Auto remove failed backends
- Recovery detection
```

### F. Caching System
```
Static File Caching:
- TTL configurable
- Cache bypass rules (cookie/header based)
- Cache size limits
- Purge/invalidation API
```

### G. Logging & Monitoring
```
Log Sources:
- Web Server Access/Error logs
- ModSecurity Audit logs
- System operation logs
- User action logs

Integration:
- ELK Stack shipping
- Real-time log viewing
- Log rotation & retention
```

### H. Alerting System
```
Channels:
- Telegram (primary) 
- Email (secondary)

Alert Types:
- Security incidents
- Performance degradation
- System overload
- Backend failures
- SSL expiration
```

### I. System Management
```
- Backup/Restore configurations
- Performance monitoring (CPU, RAM, I/O)
- User management with role-based access
- Organization/Department grouping
- Domain-based permissions
```

## CẤU TRÚC THƯ MỤC ĐỀ XUẤT

```
cloud-waf/
├── docker-compose.yml
├── .env.example
├── README.md
├── backend/
│   ├── Dockerfile
│   ├── package.json
│   ├── src/
│   │   ├── app.js
│   │   ├── config/
│   │   ├── controllers/
│   │   ├── models/
│   │   ├── routes/
│   │   ├── middleware/
│   │   ├── services/
│   │   └── utils/
│   └── migrations/
├── waf-engine/
│   ├── Dockerfile
│   ├── nginx/ (if nginx selected)
│   │   ├── nginx.conf
│   │   ├── conf.d/
│   │   └── modsecurity/
│   ├── apache/ (if apache selected)
│   │   ├── apache2.conf
│   │   ├── sites-available/
│   │   └── modsecurity/
│   └── scripts/
├── database/
│   ├── Dockerfile
│   ├── init/
│   └── backup/
├── elk/ (optional)
│   ├── elasticsearch/
│   ├── logstash/
│   └── kibana/
├── frontend/ (if separate frontend needed)
└── scripts/
    ├── setup.sh
    ├── backup.sh
    └── deploy.sh
```

## CODING STANDARDS

### Backend (Node.js)
```javascript
// Sử dụng async/await
// Error handling middleware
// Input validation với Joi/Yup
// Authentication với JWT
// Rate limiting với express-rate-limit
// Security headers với helmet
// CORS configuration
// Logging với winston
```

### Database
```sql
-- Sử dụng migrations
-- Foreign key constraints
-- Indexes cho performance
-- Audit logs cho security
-- Connection pooling
```

### Docker
```dockerfile
# Multi-stage builds
# Non-root user
# Health checks
# Environment variables
# Volume mounts cho persistent data
# Network configuration
```

## API ENDPOINTS ĐỀ XUẤT

```
Auth:
POST /api/auth/login
POST /api/auth/logout
GET  /api/auth/profile

Domains:
GET    /api/domains
POST   /api/domains
PUT    /api/domains/:id
DELETE /api/domains/:id

Security Rules:
GET    /api/security/rules
POST   /api/security/rules
PUT    /api/security/rules/:id
DELETE /api/security/rules/:id

Whitelist/Blacklist:
GET    /api/security/whitelist
POST   /api/security/whitelist
DELETE /api/security/whitelist/:id

Monitoring:
GET /api/monitoring/stats
GET /api/monitoring/logs
GET /api/monitoring/alerts

System:
POST /api/system/backup
POST /api/system/restore
GET  /api/system/health
```

## SECURITY CONSIDERATIONS

1. **Authentication & Authorization**
   - JWT tokens với refresh mechanism
   - Role-based access control (RBAC)
   - Session management
   - IP whitelisting cho admin panel

2. **Input Validation**
   - Sanitize tất cả user input
   - SQL injection prevention
   - XSS protection
   - File upload validation

3. **Secrets Management**
   - Environment variables
   - Secret rotation
   - Database encryption
   - SSL/TLS certificates

4. **Audit Trail**
   - Log tất cả admin actions
   - User access logs
   - Configuration changes
   - Security events

## DEPLOYMENT STRATEGY

1. **Development Environment**
   ```bash
   docker-compose -f docker-compose.dev.yml up
   ```

2. **Production Environment**
   ```bash
   docker-compose -f docker-compose.prod.yml up -d
   ```

3. **Slave Node Deployment**
   ```bash
   # Sync configuration từ master
   # Auto-discovery mechanism
   # Load balancing registration
   ```

## TESTING APPROACH
- **KHÔNG** tạo unnecessary test files
- Integration tests cho critical paths
- Security testing cho WAF rules
- Performance testing cho load balancing
- Manual testing cho UI workflows

## PERFORMANCE TARGETS
- Response time < 100ms cho static content
- < 500ms cho dynamic content
- Support 10,000+ concurrent connections
- 99.9% uptime
- Auto-scaling với slave nodes

---

**LƯU Ý QUAN TRỌNG**: Khi phát triển, luôn tham khảo file `requirement.md` và `description.md` để đảm bảo không thêm tính năng không cần thiết. Ưu tiên tính đơn giản và hiệu quả thay vì phức tạp hóa hệ thống.
