# 1-signals in nginx
---
### تمام سیگنال‌های Nginx (اصلی و پیشرفته) همراه با معادل دستوری، هدف پروسه و عملکرد درون بلوک کد با ساختار جدولی درخواستی شما:

| Signal | Nginx CLI Flag  | Target Process | Description / Usage (Persian)                                                                                              |
| ------ | --------------- | -------------- | -------------------------------------------------------------------------------------------------------------------------- |
| QUIT   | nginx -s quit   | Master         | ا-<mark>Graceful Shutdown</mark>: پایان تمیز سرویس پس از اتمام پردازش درخواست‌های در جریان (بدون Drop شدن کلاینت‌ها).      |
| TERM   | nginx -s stop   | Master         | ا-<mark>Fast / Immediate Shutdown</mark> : بستن فوری و متوقف کردن تمام پردازش‌ها بدون معطلی برای درخواست‌ها.               |
| INT    | nginx -s stop   | Master         | مشابه TERM عمل می‌کند؛ بستن آنی و سریع سرویس.                                                                              |
| HUP    | nginx -s reload | Master         | ا-<mark>Reload Config:</mark>  اعمال کانفیگ جدید بدون قطعی، اجرای Workerهای جدید و بستن تدریجی Workerهای قدیمی.            |
| USR1   | nginx -s reopen | Master         | ا-<mark>Reopen Logs</mark>: بستن و بازگشایی مجدد فایل‌های لاگ (کاربرد در Log Rotation بدون ریستارت).                       |
| USR2   | -               | Master         | ا-<mark>Live Upgrade:</mark> آپگرید باینری Nginx حین کار بدون قطعی (اجرای نسخه جدید در کنار Master قبلی).                  |
| WINCH  | -               | Master         | ا-<mark>Graceful Worker Stop</mark>: توقف آرام تمام Worker Processها (معمولاً در مرحله پایانی آپگرید زنده استفاده می‌شود). |

---

### 📌 راهنمای عملکرد سیگنال‌ها (Key Actions Breakdown)

* **Graceful Shutdown (`QUIT`):**  
  پایان تمیز سرویس پس از اتمام پردازش درخواست‌های در جریان (بدون Drop شدن کلاینت‌ها).

* **Fast / Immediate Shutdown (`TERM` / `INT`):**  
  بستن فوری و متوقف کردن تمام پردازش‌ها بدون معطلی برای درخواست‌ها.

* **Reload Config (`HUP`):**  
  اعمال کانفیگ جدید بدون قطعی، اجرای Workerهای جدید و بستن تدریجی Workerهای قدیمی.

* **Reopen Logs (`USR1`):**  
  بستن و بازگشایی مجدد فایل‌های لاگ (کاربرد در Log Rotation بدون ریستارت).

* **Live Upgrade (`USR2`):**  
  آپگرید باینری Nginx حین کار بدون قطعی (اجرای نسخه جدید در کنار Master قبلی).

* **Graceful Worker Stop (`WINCH`):**  
  توقف آرام تمام Worker Processها (معمولاً در مرحله پایانی آپگرید زنده استفاده می‌شود).

