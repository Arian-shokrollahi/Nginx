# 1-context
# 1-زمینه یا کانتکست در کانفیگ nginx
---
# کانتکست(context)

کانفیگ Nginx یک ساختار درختی و سلسله‌مراتبی (Hierarchical) دارد. به هر سطح یا محدوده که تنظیمات درون آن اثر می‌گذارد **Context** (زمینه یا کانتکست) می‌گویند.

کانتکست‌ها دقیقاً به همین ترتیب از بالا به پایین و تودرتو (Nested) قرار می‌گیرند:

---

# سلسله‌مراتب کانتکست‌های Nginx (از کل به جزء)

1. ا-**Main Context (Global):**
   - **توضیح:** بالاترین سطح فایل کانفیگ (خارج از هرگونه آکولاد `{ }`).
   - **وظیفه:** مدیریت تنظیمات عمومی سیستم‌عامل، یوزر اجراکننده، مسیر فایل PID و تعداد پروسس‌ها.
   - **نمونه دایرکتیوها:** `user`, `worker_processes`, `pid`

2. ا- **Events Context (`events { }`):**
   - **توضیح:** بلاک تنظیمات لایه کانکشن و مدیریت رویدادها.
   - **وظیفه:** تعیین نحوه پردازش و حداکثر اتصالات همزمان شبکه توسط هر Worker Process.
   - **نمونه دایرکتیوها:** `worker_connections`, `multi_accept`

3. ا-**HTTP Context (`http { }`):**
   - **توضیح:** کانتکست والد برای تمامی تنظیمات وب‌سرور و پروتکل وب.
   - **وظیفه:** تعریف لاگ‌ها، کش، انواع فایل‌ها (MIME Types)، ماژول‌های فشرده‌سازی (Gzip) و تنظیمات پایه TCP.
   - **نمونه دایرکتیوها:** `include`, `sendfile`, `gzip`, `access_log`

3. ا-**Server Context (`server { }`):**
   - **توضیح:** تعریف Virtual Hostها (معمولاً داخل بلاک `http { }`).
   - **وظیفه:** مدیریت و پیکربندی اختصاصی یک دامنه، ساب‌دامین یا پورت شبکه خاص.
   - **نمونه دایرکتیوها:** `listen`, `server_name`, `ssl_certificate`

3. ا-**Location Context (`location { }`):**
   - **توضیح:** مسیریابی URLها (Routing) که درون بلاک `server { }` قرار می‌گیرد.
   - **وظیفه:** تعیین رفتار وب‌سرور در برابر آدرس‌ها و URIهای درخواستی کلاینت (مثلاً سرو فایل، پروکسی و...).
   - **نمونه دایرکتیوها:** `root`, `index`, `proxy_pass`, `try_files`

3. ا-**Upstream Context (`upstream { }`):**
   - **توضیح:** تعریف دسته‌ای از سرورهای بک‌اند (معمولاً درون بلاک `http { }`).
   - **وظیفه:** توزیع بار (Load Balancing) و هدایت درخواست‌ها به چند سرور یا سرویس مختلف.
   - **نمونه دایرکتیوها:** `server`, `least_conn`, `keepalive`

---
## ۲. ساختار کدی (شکل چیدمان در `nginx.conf`)

```bash
# 1. Main Context (بالاترین سطح)
user www-data;
worker_processes auto;
pid /run/nginx.pid;

# 2. Events Context
events {
    worker_connections 1024;
}

# 3. HTTP Context
http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    # 6. Upstream Context (اختیاری - برای لود بالانسر یا بک‌اند)
    upstream backend_servers {
        server 127.0.0.1:8000;
        server 127.0.0.1:8001;
    }

    # 4. Server Context (یک سایت یا دامنه)
    server {
        listen 80;
        server_name example.com;

        # 5. Location Context (یک مسیر خاص در سایت)
        location / {
            root /var/www/html;
            index index.html;
        }

        # یک Location دیگر
        location /api/ {
            proxy_pass http://backend_servers;
        }
    }
}

```

---
### ۳. مفهوم ارث‌بری (Inheritance) - خیلی مهم!

در Nginx قانون طلایی این است: **بچه‌ها خصوصیات پدرشان را به ارث می‌برند، مگر اینکه خودشان آن را تغییر دهند.**

- اگر تنظیمی را در `http` بنویسی، به تمام `server`ها و `location`ها ارث می‌رسد.
- اگر همان تنظیم را داخل یک `server` دوباره با مقدار جدید بنویسی، فقط برای همان دامنه مقدارش عوض می‌شود (Override می‌شود).
