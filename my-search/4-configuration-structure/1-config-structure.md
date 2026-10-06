# 1-configuration file structure 
# 1- ساختار فایل کانفیگ
---
## فایل هایه کانفیگ درون nginx به چه صورت است؟
فایل کانفیگ Nginx به صورت **درختی و موروثی (Hierarchical)** چیده شده است:

1. **انواع Directive:**
    
    - ا-<mark>**Simple Directive</mark>:** تک‌خطی است و حتماً با `;` تمام می‌شود (مانند `worker_processes 1;` یا `listen 80;`).
    - ا-<mark>**Block Directive / Context:**</mark> چندخطی است و با `{}` مشخص می‌شود و کانتکست‌های فرزند را در خود نگه می‌دارد.
2. **سلسله‌مراتب کانتکست‌ها (Context Hierarchy):**
```
main (Global Context)
 ├── user, worker_processes, pid, error_log
 ├── events { ... }
 └── http {
      ├── include, default_type, access_log
      └── server {
           ├── listen, server_name
           └── location / {
                └── root, index;
           }
      }
 }
```
3. نمونه کد نمونه با نشانه‌گذاری ساختار:
```bash
# 1. Main Context (محیط سراسری)
user nginx;
worker_processes auto;

# 2. Events Context (تنظیمات ارتباطات شبکه)
events {
    worker_connections 1024; # Simple directive
}

# 3. HTTP Context (تنظیمات وب و پروکسی)
http {
    include mime.types;

    # 4. Server Context (تنظیمات یک هاست/دامنه خاص)
    server {
        listen 80;
        server_name example.com;

        # 5. Location Context (مسیردهی URL)
        location / {
            root /usr/share/nginx/html;
            index index.html;
        }
    }
}

```
