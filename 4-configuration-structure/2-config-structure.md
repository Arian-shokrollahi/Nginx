# 2-config structure part2
---
## ساختار فایل پیکربندی اصلی nginx(/etc/nginx/nginx.conf/)
##### -وب سرور nginx به این صورت است که ساختار سلسله مراتبی (Hierarchical) دارد و درختی است و تنظیمات nginx به این دو جز اصلی تقسیم میشوند:

- 1-ا-<mark>دایرکتیو(Directive)(دستورالعمل ها)</mark>--->تنظیمات خطی یا چند خطی که یک مقدار میگیرند و با ;(semicolon)جدا میشوند.
- 2-ا-<mark>ماژول (module)</mark> ماژول هم داریم که جلوتر درموردش صحبت میکنیم 
- 2-ا-<mark>زمینه یا بلاک ها(Contexts)</mark>----> بخش‌هایی که با آکولاد `{ ... }` مشخص می‌شوند و دایرکتیوها یا بلاک‌های دیگر را داخل خود جای می‌دهند.

---
## معماری و سلسله‌مراتب کانتکست‌ها (Hierarchy)

```bash
┌──────────────────────────────────────────────────────────┐
│ Main Context (Global)                                    │
│ ├─ user, worker_processes, error_log, pid                │
│ │                                                        │
│ ├─ events { ... }                                        │
│ │                                                        │
│ └─ http {                                                │
│      ├─ Directives (access_log, keepalive_timeout, ...)  │
│      │                                                   │
│      ├─ server { (Virtual Host 1)                        │
│      │    ├─ listen, server_name                         │
│      │    ├─ location / { ... }                          │
│      │    └─ location /api { ... }                       │
│      │  }                                                │
│      │                                                   │
│      └─ server { (Virtual Host 2)                        │
│           └─ ...                                         │
│         }                                                │
│    }                                                     │
└──────────────────────────────────────────────────────────┘

```

| Context           | نوع       | توضیحات و کاربرد                                                                                 | نمونه دایرکتیوهای رایج                         |
| :---------------- | :-------- | :----------------------------------------------------------------------------------------------- | :--------------------------------------------- |
| **Main (Global)** | Root      | بالاترین سطح؛ خارج از تمام بلاک‌های `{}` قرار دارد. تنظیمات کل پروسه Nginx اینجاست.              | `user`, `worker_processes`, `pid`, `error_log` |
| **events**        | Block     | تنظیمات لایه شبکه و نحوه مدیریت Connectionها توسط workerها.                                      | `worker_connections`, `use epoll`              |
| **http**          | Block     | مدیریت پروتکل HTTP/HTTPS و پردازش وب؛ شامل تمام تنظیمات وب‌سرور، کش، لاگ و کشیدن بقیه کانفیگ‌ها. | `include`, `gzip`, `sendfile`, `access_log`    |
| **server**        | Sub-block | داخل `http` تعریف می‌شود؛ نمایانگر یک Virtual Host (دامنه یا پورت مشخص).                         | `listen`, `server_name`, `ssl_certificate`     |
| **location**      | Sub-block | داخل `server` تعریف می‌شود؛ مشخص می‌کند با URIهای مختلف چه رفتاری شود (Routing).                 | `root`, `proxy_pass`, `try_files`, `index`     |
| **stream**        | Block     | در سطح Main قرار می‌گیرد؛ برای پروکسی و لودبالانسینگ لایه ۴ (TCP/UDP).                           | `server`, `listen`, `proxy_pass`               |
