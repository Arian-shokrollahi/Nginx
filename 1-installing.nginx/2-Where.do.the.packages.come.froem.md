# مخزن هایه پیش فرض از کجا میان
---
## یه سوال اساسی اگر مخزن هایه پیش فرض مستقیم از رپو سازنده اون برنامه نیستند از کجا میان
- دلیل پیش امدن سوال مثلا برایه اینکه بخواید وب سرور nginx رو بریزید چند مدل دارد ولی مدل 
```bash
sudo apt update
sudo apt install nginx 
```
- پیشنهاد نمیشه چرا؟
- دقیقاً یک نکته کلیدی در دنیای لینوکس همین‌جاست: **هر چیزی که در مخزن پیش‌فرض سیستم‌عاملت وجود دارد، لزوماً از مخزن رسمی سازنده برنامه (Upstream) دانلود نمی‌شود!** 
- مثلا من در کالی لینوکس هستم و میزنم 
- برای پیدا کردن اطلاعات اون بسته sudo apt-cache policy nginx
```bash
nginx: 
Installed: (none) 
Candidate: 1.30.1-3
 Version table: 
 1.30.1-3 500 
 500 http://http.kali.org/kali kali-last-snapshot/main amd64
```
- اگر به خط اخر نگاه کنید نوشته 
```
500 http://http.kali.org/kali kali-last-snapshot/main amd64 Packages

```
- این خط نشان می‌دهد که این پکیج از **مخزن رسمی کالی لینوکس (Kali Linux Repositories)** می‌آید، نه از سایت رسمی `nginx.org`.
---
### پکیج‌ها چطور به مخزن توزیع‌ها (مثل Ubuntu / Debian / Kali) می‌رسند؟

در دنیای لینوکس زنجیره انتشار به این شکل است:

[۱. تیم سازنده اصلی Nginx]
       │ (سورس کد را می‌نویسند و منتشر می‌کنند)
       ▼
[۲. تیم دبیان / کالی / اوبونتو]
       │ (سورس را می‌گیرند، با استانداردهای توزیع خودشان کامپایل و تست می‌کنند و پکیج deb. می‌سازند)
       ▼
[۳. سرور مخازن کالی: http.kali.org]
       │ (در مخزن رسمی کالی قرار می‌گیرد)
       ▼
[۴. سیستم لینوکس شما]
- **تیم توسعه‌دهنده توزیع (Maintainers):** سورس‌کد Nginx را از سازنده می‌گیرند، پچ‌های امنیتی یا هماهنگی سیستم خودشان را روی آن اعمال می‌کنند، آن را تبدیل به پکیج `.deb` می‌کنند و روی سرورهای خودشان (اینجا `http.kali.org`) قرار می‌دهند.
- پس تو داری نسخه پکیج‌شده توسط **تیم کالی (یا اوبونتو)** را دریافت می‌کنی.

---
# مثال برایه دیدن اینکه بعد رفتن روش دوم یا ریختن از مخزن اصلی nginx وقتی apt-cache بگیریم نشون میده که از مخزن رسمی nginx میاد
1- اول اینه که این نشون دهنده ی این است که این وب سرور توسط تین ابونتو درون مخزن ها قرار داده شده
```bash
sudo apt-cache policy nginx
nginx:
  Installed: 1.30.4-1~resolute
  Candidate: 1.30.4-1~resolute
  Version table:
 *** 1.30.4-1~resolute 100
        100 /var/lib/dpkg/status
     1.28.3-2ubuntu1.10 500
        500 http://archive.ubuntu.com/ubuntu resolute-updates/main amd64 Packages
        500 http://security.ubuntu.com/ubuntu resolute-security/main amd64 Packages
```
- 2-بعد از رفتن به nginx.org بعد documentation بعد installing بعد ubuntu اون کد هارو به ترتیب بزند
```bash
# نصب پیش‌نیازها
sudo apt install curl gnupg2 ca-certificates lsb-release ubuntu-keyring

# دریافت کلید احراز هویت Nginx
curl https://nginx.org/keys/nginx_signing.key | gpg --dearmor \
    | sudo tee /usr/share/keyrings/nginx-archive-keyring.gpg >/dev/null

# اضافه کردن ریپازیتوری Nginx
echo "deb [signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] \
http://nginx.org/packages/ubuntu `lsb_release -cs` nginx" \
    | sudo tee /etc/apt/sources.list.d/nginx.list

# آپدیت و نصب
sudo apt update
sudo apt install nginx

```
3-و اگر دوباره sudo apt-cache policy nginx رو بزنید خط اخر نوشته nginx.org که این نشون دهندهیه اینه که مخزن اصلی nginx رو ما اد کردیم به لیست مخزن هایه اضافه شده 
- ls -l /etc/apt/sources.list.d/
- اگر این دستور رو بزنید میبینید در کنار ubuntu.sources مخزن nginx هم اضافه شده
