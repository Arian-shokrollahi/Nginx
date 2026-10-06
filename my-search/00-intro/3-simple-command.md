# 03-دستورات ساده nginx که باید بلد باشید

---
# اگر از ماشین مجازی استفاده میکنید و اون توزیعی که نصب میکنید جدید است و systemd رویه اون فعاله این دستورات پایین رو پیشنهاد میدم

| دستور | کاربرد |
|---|---|
| `sudo systemctl start nginx` | اجرای Nginx همین الان |
| `sudo systemctl enable nginx` | اجرای خودکار Nginx هنگام Boot |
| `sudo systemctl enable --now nginx` | اجرای فوری + فعال‌سازی هنگام Boot |
| `sudo systemctl status nginx` | مشاهده وضعیت Nginx |
| `sudo nginx -t` | تست صحت کانفیگ |
| `sudo systemctl reload nginx` | اعمال تغییرات کانفیگ بدون خاموشی کامل |
| `sudo systemctl stop nginx` | متوقف کردن Nginx |
| `sudo systemctl restart nginx` | توقف و اجرای دوباره Nginx |

---
# اگر قدیمی است و systemd روی اون فعال نیست باید از سرویس استفاده

| دستور `systemctl` | دستور `service` | کاربرد |
|---|---|---|
| `sudo systemctl start nginx` | `sudo service nginx start` | اجرای Nginx همین الان |
| `sudo systemctl enable nginx` | معادل مستقیم ندارد | اجرای خودکار هنگام Boot |
| `sudo systemctl enable --now nginx` | معادل مستقیم ندارد | اجرای فوری + فعال‌سازی هنگام Boot |
| `sudo systemctl status nginx` | `sudo service nginx status` | مشاهده وضعیت Nginx |
| `sudo nginx -t` | `sudo nginx -t` | تست صحت کانفیگ |
| `sudo systemctl reload nginx` | `sudo service nginx reload` | اعمال تغییرات کانفیگ بدون خاموشی کامل |
| `sudo systemctl stop nginx` | `sudo service nginx stop` | متوقف کردن Nginx |
| `sudo systemctl restart nginx` | `sudo service nginx restart` | توقف و اجرای دوباره Nginx |


