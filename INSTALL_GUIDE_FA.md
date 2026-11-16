# راهنمای نصب و استفاده از اسکیل حسابفا (Hesabfa) در Hermes Agent

این راهنما مراحل نصب، راه‌اندازی و استفاده از اسکیل `hesabfa-accounting` را روی سیستم شخصی شما توضیح می‌دهد.

## پیش‌نیازها

- **Python 3.8+** روی سیستم نصب باشد.
- **Hermes Agent** نصب شده باشد (آخرین نسخه).
- یک کسب‌وکار ثبت‌شده در حسابfa با دسترسی API.

---

## مرحله ۱ — دریافت فایل‌های اسکیل

### روش الف: دانلود ZIP و استخراج

```bash
# فایل زیپ را دریافت کنید
curl -L -o hesabfa-skill.zip <URL_دانلود>

# استخراج در پوشه دلخواه
unzip hesabfa-skill.zip -d ~/hesabfa-skill
```

### روش ب: کلون از گیت‌هاب

```bash
git clone https://github.com/<REPO>/hesabfa-accounting.git
cd hesabfa-accounting
```

---

## مرحله ۲ — کپی اسکیل به پوشه Hermes Agent

فایل `SKILL.md` را به مسیر اسکิล‌های Hermes کپی کنید:

```bash
# پیدا کردن مسیر اسکیل‌های Hermes
echo $HERMES_SKILL_DIR

# اگر ست نشده بود، معمولاً یکی از مسیرهای زیر است:
# macOS/Linux:  ~/.hermes/skills/productivity/
# Windows:      %LOCALAPPDATA%\hermes\profiles\nine\skills\productivity\

# کپی فایل SKILL.md
mkdir -p ~/.hermes/skills/productivity/hesabfa
cp SKILL.md ~/.hermes/skills/productivity/hesabfa/SKILL.md
cp scripts/hesabfa.py   ~/.hermes/skills/productivity/hesabfa/scripts/
cp references/         ~/.hermes/skills/productivity/hesabfa/references/ -r
```

> **نکته:** نام پوشه اسکیل (`hesabfa`) باید دقیقاً مطابق فیلد `name` در SKILL.md باشد.

---

## مرحله ۳ — کسب اعتبارنامه حسابfa

1. وارد پنل حسابfa شوید: [hesabfa.com](https://www.hesabfa.com)
2. به مسیر **تنظیمات > تنظیمات مالی > API** بروید.
3. دو مقدار لازم دارید:
   - **apiKey** — کلید عمومی دسترسی
   - **loginToken** — توکن ورود موقتی (یا `userId` + `password` مالک)
4. این مقادیر را جایزه ندارید — هرگز آن‌ها را در فایل متنی نگذارید.

---

## مرحله ۴ — ست کردن متغیرهای محیطی

در همان terminal که Hermes Agent را اجرا می‌کنید، متغیرها را ست کنید:

```bash
export HESABFA_API_KEY="کلید-API-شما"
export HESABFA_LOGIN_TOKEN="توکن-ورود-شما"
```

> برای Windows PowerShell:
> ```powershell
> $env:HESABFA_API_KEY = "کلید-API-شما"
> $env:HESABFA_LOGIN_TOKEN = "توکن-ورود-شما"
> ```

---

## مرحله ۵ — تست اتصال (آفلاین)

قبل از ارسال اولین درخواست، تست خودکار را اجرا کنید:

```bash
python3 "$(hesabfa)/scripts/hesabfa.py" selftest
```

خروجی موفق:
```
خودآزمایی موفق: تبدیل تاریخ شمسی (به وقت تهران)، queryInfo، ترجمه‌ی search به فلتر و enum ها سالم هستند.
```

اگر خطایی دیدید، Python و مسیر اسکریپت را بررسی کنید.

---

## نحوه استفاده — مثال‌های کاربردی

### جستجوی مشتری
```bash
python3 "$(hesabfa)/scripts/hesabfa.py" contact list \
  --search "شرکت آریا" \
  --take 50
```

### ثبت مشتری جدید
```bash
python3 "$(hesabfa)/scripts/hesabfa.py" contact save \
  --name "شرکت آریا" \
  --company "شرکت آریا" \
  --contact-type حقوقی \
  --is-customer \
  --mobile "09121234567" \
  --node-family "اشخاص:اشخاص حقوقی:مشتریان"
```

### صدور فاکتور فروش
```json
{
  "date": "1404/08/23",
  "dueDate": "1404/09/23",
  "contactCode": "0001",
  "invoiceType": "فروش",
  "status": 1,
  "invoiceItems": [
    {
      "itemCode": "K001",
      "description": "کابل شبکه Cat6",
      "quantity": 2,
      "unitPrice": 950000,
      "discount": 100000,
      "tax": 90000
    }
  ]
}
```
```bash
python3 "$(hesabfa)/scripts/hesabfa.py" invoice save \
  --file /tmp/invoice.json
```

### دریافت گزارش تراز آزمایشی
```bash
python3 "$(hesabfa)/scripts/hesabfa.py" report trial-balance \
  --start-date "1404/01/01" \
  --end-date "1404/08/29"
```

### تست dry-run (بدون ارسال)
```bash
python3 "$(hesabfa)/scripts/hesabfa.py" invoice save \
  --file /tmp/invoice.json \
  --dry-run
```

---

## ساختار پوشه‌ها پس از نصب

```
~/.hermes/skills/productivity/hesabfa/
├── SKILL.md                    # مستندات اصلی اسکیل
├── scripts/
│   └── hesabfa.py              # رابط CLI (self-contained)
├── references/
│   ├── api-reference.md        # ۹۲ متد API
│   ├── enums-errors.md         # جداول enum و کدهای خطا
│   ├── objects.md              # ساختار اشیای JSON
│   └── recipes.md              # گردش‌کارهای رایج
└── assets/                     # (اختیاری) تصاویر/لوگو
```

---

## نکات امنیتی مهم

1. **هرگز** apiKey و loginToken را در فایل قرار ندهید.
2. متغیرهای محیطی فقط در session terminal جاری معتبرند.
3. خروجی `--dry-run` کلیدها را ماسک می‌کند (`abcd…`) — خیالتان راحت باشد.
4. قبل از هر `delete` از کاربر تأیید صریح بگیرید.

---

## رفع اشکال

| مشکل | راه‌حل |
|------|--------|
| `exit code 4: apiKey تنظیم نشده` | متغیر `HESABFA_API_KEY` را ست کنید |
| `exit code 2: HTTP 403` | loginToken منقضی شده — از پنل حسابfa جدید بگیرید |
| `exit code 101: TooManyRequests` | ۱۰ ثانیه صبر کنید (CLI خودش retry می‌کند) |
| `خطای 199: Invalid login token` | توکن را مجدداً از پنل حسابfa دریافت کنید |
| `SystemExit: فرمت تاریخ نامعتبر` | از فرمت `1404/08/23` یا `2025-11-14` استفاده کنید |

---

## منبع

- مستندات رسمی حسابfa: [https://www.hesabfa.com/help/api](https://www.hesabfa.com/help/api)
- پروژه GitHub: `<LINK_REPO>`
