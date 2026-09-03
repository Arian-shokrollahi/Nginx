# 2-Where is the worker and master configuration?
# 2- پیکربندی پردازه هایه worker و master کجاست؟
---
تنظیمات مربوط به **Master Process** و **Worker Process** دقیقاً در **بالاترین سطح فایل کانفیگ اصلی <mark>(`/etc/nginx/nginx.conf`)</mark> ** نوشته می‌شوند که اصطلاحاً به آن **Main Context** یا محیط سراسری می‌گویند (یعنی بیرون از هرگونه بلاک مثل `{ } http` یا `{ } events`).

- نمونه کانفیگ در فایل `/etc/nginx/nginx.conf`:

```
# ==========================================
# Main Context (محل تنظیم Master و Workerها)
# ==========================================

# کاربری که Workerها با دسترسی آن کار می‌کنند:
user www-data;

# تعداد Worker Processها (تنظیم دستی یا خودکار):
worker_processes auto;

# فایلی که Process ID (PID) مربوط به Master Process در آن ذخیره می‌شود:
pid /run/nginx.pid;

# اولویت CPU برای Worker Processها (اختیاری):
worker_priority 0;

# مسیر لاگ خطاهای سراسری:
error_log /var/log/nginx/error.log warn;


# ==========================================
# سایر بلاک‌ها بعد از این تنظیمات می‌آیند:
# ==========================================
events {
    worker_connections 1024;
}

http {
    # تنظیمات وب‌سرور...
}

```

---

| دستور (Directive)          | کاربرد و وظیفه                                       | روی کدام پروسس تأثیر دارد؟ |
| -------------------------- | ---------------------------------------------------- | -------------------------- |
| `worker_processes auto;`   | تعیین تعداد Workerهای فعال در سیستم                  | Worker Process             |
| `user www-data;`           | تعیین کاربر لینوکسی که ورکرها با آن اجرا می‌شوند     | Worker Process             |
| `pid /run/nginx.pid;`      | مسیر فایلی که شماره PID مستر در آن ذخیره می‌شود      | Master Process             |
| `worker_connections 1024;` | حداکثر کانکشن همزمان برای هر ورکر (درون بلاک events) | Worker Process             |
| `worker_cpu_affinity`      | اختصاص دادن دستی هر ورکر به یک هسته خاص از CPU       | Worker Process             |

