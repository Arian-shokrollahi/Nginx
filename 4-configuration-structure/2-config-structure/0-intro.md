# 0-intro
---

# اینم نکته ای که باید دقت کنید
- ساختار معماری فایل‌های کانفیگ Nginx (مثل `nginx.conf`) تماماً بر پایه همین دو مفهوم چیده شده:

1. ا-<mark>**Simple Directive**</mark>: دستورات خطی که با سمی‌کولن (`;`) بسته می‌شوند (مثل `user www-data;` یا `keepalive_timeout 65;`).
2. ا<mark>-**Block Directive**:</mark> بلوک‌های ساختاری که با آکولاد (`{ }`) باز و بسته می‌شوند و یک محیط یا **Context** برای سایر دستورات می‌سازند (مثل `http { }`، `server { }` و `location { }`).

هر دستوری در Nginx یا خودش یک پارامترِ ساده است، یا فضایی (کانتکست) است که دستورات دیگر را در یک محدوده مشخص سازماندهی و اجرا می‌کند.

#### -->در فایل هایه بعدی من بهتون درمورد این دو میگویم و اگر این دو رو خوب بفهمید یعنی کل nginx رو خوب میفهمید.

### -->و میریم الان درمورد هرکدوم از این ها درون یه فولدر جدا درمورد 
- 1-یک فولدر برایه -->simple directive
- 2-یک فولدر برایه-->block directive

---
- بلوک‌های سازنده کانفیگ (Directives Structure)

| Component                  | Syntax Format                  | Golden Rule                                     | Example                                            |
|----------------------------|--------------------------------|-------------------------------------------------|----------------------------------------------------|
| Simple Directive           | name param1 param2;            | حتماً باید با سمی‌کالن (;) پایان یابد.          | listen 80; / worker_processes auto;               |
| Block Directive / Context  | name { ... }                   | با آکولاد ({}) باز و بسته شده و دربرگیرنده است.  | http { ... } / server { ... }                      |

- وظایف کانتکست‌های اصلی (Context Responsibilities)

| Context    | Scope Level  | Primary Responsibility                         | Common Directives                                  |
|------------|--------------|------------------------------------------------|----------------------------------------------------|
| main       | Global (OS)  | تنظیمات پایه پروسه‌ها، یوزر و فایل لاگ سراسری | user, worker_processes, pid, error_log             |
| events     | Network Core | تنظیمات حلقه پردازش شبکه و سقف اتصالات همزمان | worker_connections, multi_accept, use epoll        |
| http       | Layer 7      | تنظیمات پروتکل وب، فشرده‌سازی، کش و لاگ سایت‌ها | include, gzip, access_log, sendfile                |
| server     | Virtual Host | پیکربندی یک دامنه/سایت مشخص یا یک پورت خاص      | listen, server_name, ssl_certificate, root         |
| location   | URI Routing  | مسیریابی بر اساس آدرس URL درخواستی کاربر        | proxy_pass, try_files, root, fastcgi_pass          |
| upstream   | Load Balancer| معرفی کلاستر سرورهای بک‌اند برای تقسیم بار      | server 10.0.0.1:8080, least_conn, keepalive        |
| stream     | Layer 4      | مدیریت و پروکسی ترافیک خام TCP / UDP           | listen 3306, proxy_pass backend_db                 |

- تقدم و اولویت انطباق مسیرها (Location Matching Priority)

| Priority | Modifier | Match Type                       | Example Match                | Description                                       |
|:--------:|:--------:|----------------------------------|------------------------------|---------------------------------------------------|
| 1        | =        | Exact Match                      | location = /favicon.ico      | تطبیق کاملاً دقیق؛ بیشترین اولویت بدون بررسی بقیه |
| 2        | ^~       | Preferential Prefix              | location ^~ /images/         | تطبیق پیشوندی با توقف بررسی عبارات منظم (Regex)   |
| 3        | ~        | Case-Sensitive Regex             | location ~ \.(php)$          | بررسی با عبارت منظم (حساس به حروف کوچک/بزرگ)       |
| 4        | ~*       | Case-Insensitive Regex           | location ~* \.(jpg\|png)$    | بررسی با عبارت منظم (غیرحساس به حروف کوچک/بزرگ)   |
| 5        | (None)   | Longest Prefix Match             | location /api/               | بیشترین تطابق پیشوند متنی ساده (کمترین اولویت)    |

- قوانین ارث‌بری و رفتار تنظیمات (Inheritance Rules)

| Inheritance Type      | Behavior Scope                                 | Mechanism & Effect                                                                     |
|-----------------------|------------------------------------------------|----------------------------------------------------------------------------------------|
| Standard Inherit      | http -> server -> location                     | تنظیمات والد به طور خودکار به فرزندان منتقل می‌شوند.                                   |
| Override              | Child Context                                  | تعریف مجدد یک دستور در فرزند، مقدار والد را بازنویسی و باطل می‌کند.                    |
| Array / Additive Trap | add_header, proxy_set_header                   | در صورت تعریف در فرزند، تمام هدرهای والد لغو شده و فقط مقدار فرزند اعمال می‌شود.        |
