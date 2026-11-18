# راهنمای نصب و استفاده از اسکیل حسابفا (Hesabfa) در Hermes Agent

این راهنما مراحل نصب، راه‌اندازی و استفاده از اسکیل `hesabfa-accounting` را روی سیستم شخصی شما توضیح می‌دهد. با نصب این مهارت، هرمس می‌تواند به بیش از ۹۲ متد API حسابفا متصل شده و امور حسابداری را با زبان محاوره‌ای مدیریت کند.

## 📋 پیش‌نیازها

پیش از شروع، باید موارد زیر روی سیستم شما نصب باشند. در ادامه دستورات نصب برای هر سیستم‌عامل آورده شده است:

### 💻 نصب پیش‌نیازها در ویندوز (PowerShell)
ترمینال PowerShell را به عنوان Administrator (مدیر) باز کنید و دستورات زیر را اجرا کنید:
```powershell
# ۱. نصب پایتون (Python 3)
winget install --id Python.Python.3 -e --source winget

# ۲. نصب گیت (Git)
winget install --id Git.Git -e --source winget

# ۳. نصب Hermes Agent
iex (irm https://hermes-agent.nousresearch.com/install.ps1)
```

### 🍏 نصب پیش‌نیازها در macOS
ترمینال (Terminal) را باز کنید و دستورات زیر را اجرا کنید:
```bash
# ۱. نصب Homebrew (در صورت عدم نصب)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# ۲. نصب پایتون و گیت
brew install python git

# ۳. نصب Hermes Agent
curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash
```

### 🐧 نصب پیش‌نیازها در لینوکس
ترمینال را باز کنید و بر اساس توزیع خود دستورات زیر را اجرا کنید:
*   **Debian / Ubuntu:**
    ```bash
    sudo apt update
    sudo apt install -y python3 python3-pip git
    ```
*   **Fedora:**
    ```bash
    sudo dnf install -y python3 git
    ```
*   **Arch Linux:**
    ```bash
    sudo pacman -S python git
    ```
**نصب Hermes Agent (لینوکس / WSL2):**
```bash
curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash
```

> ⚠️ **نکته مهم:** پس از نصب پیش‌نیازها، حتماً ترمینال را ببندید و یک ترمینال جدید باز کنید تا دستورات شناسایی شوند.

---

## 🚀 مرحله ۱ — دریافت فایل‌های اسکیل

### روش الف: دانلود مستقیم فایل ZIP
با دستورات زیر، فایل `hesabfa-accounting.zip` مستقیماً در همان مسیری که ترمینال باز است، دانلود می‌شود:

**💻 در ویندوز (PowerShell):**
```powershell
curl.exe -L -O https://github.com/msnwoodpainting/hesabfa-accounting/raw/main/hesabfa-accounting.zip
```

**🐧 در لینوکس و 🍏 مک (Terminal):**
```bash
curl -L -O https://github.com/msnwoodpainting/hesabfa-accounting/raw/main/hesabfa-accounting.zip
# یا با استفاده از wget:
# wget https://github.com/msnwoodpainting/hesabfa-accounting/raw/main/hesabfa-accounting.zip
```

### روش ب: کلون از گیت‌هاب (مناسب توسعه‌دهندگان)
```bash
git clone https://github.com/msnwoodpainting/hesabfa-accounting.git
cd hesabfa-accounting
```

---

## 📂 مرحله ۲ — کپی اسکیل به پوشه Hermes Agent

فایل `hesabfa-accounting.zip` را از حالت زیپ خارج کنید و پوشه `hesabfa-accounting` را در مسیر اسکیل‌های Hermes کپی کنید.

**مسیرهای پیش‌فرض اسکیل‌ها در هرمس:**

- **اگر پروفایل شخصی ندارید:**
  `C:\Users\<YourUsername>\AppData\Local\hermes\skills\hesabfa-accounting`
  *(گاهی هرمس آن را در پوشه `productivity` ذخیره می‌کند: `...\hermes\skills\productivity\hesabfa-accounting`)*

- **اگر از پروفایل شخصی استفاده می‌کنید:**
  `C:\Users\<YourUsername>\AppData\Local\hermes\profiles\<Profilename>\skills\hesabfa-accounting`
  *(در لینوکس و مک: `~/.local/share/hermes/profiles/<Profilename>/skills/`)*

> 💡 **روش خودکار:** می‌توانید فایل زیپ را به هرمس بدهید و بگویید: *"این یک اسکیل است با نام hesabfa-accounting، آن را در مسیر پروفایل `<Profilename>` برای من نصب کن."*

> **نکته مهم:** نام پوشه اسکیل (`hesabfa-accounting`) باید دقیقاً مطابق فیلد `name` در فایل `SKILL.md` باشد.

---

## 🔑 مرحله ۳ — کسب اعتبارنامه حسابفا

1. وارد پنل حسابفا شوید: ([https://app.hesabfa.com](https://app.hesabfa.com))
2. به مسیر **تنظیمات > تنظیمات مالی > API** بروید.
3. دو مقدار لازم دارید:
   - **apiKey** — کلید عمومی دسترسی
   - **loginToken** — توکن ورود موقت (یا `userId` + `password` مالک)

> ⚠️ **هشدار امنیتی:** هرگز این مقادیر را در فایل‌های متنی، کدهای گیت‌هاب یا جاهای ناامن قرار ندهید.

---

## ⚙️ مرحله ۴ — ست کردن متغیرهای محیطی

در همان ترمینالی که Hermes Agent را اجرا می‌کنید، متغیرها را ست کنید:

**🐧 در لینوکس و 🍏 مک (Terminal):**
```bash
export HESABFA_API_KEY="کلید-API-شما"
export HESABFA_LOGIN_TOKEN="توکن-ورود-شما"
```

**💻 در ویندوز (PowerShell):**
```powershell
$env:HESABFA_API_KEY = "کلید-API-شما"
$env:HESABFA_LOGIN_TOKEN = "توکن-ورود-شما"
```

---

## 🧪 مرحله ۵ — تست اتصال (آفلاین)

قبل از ارسال اولین درخواست، تست خودکار را اجرا کنید. ابتدا وارد پوشه اسکیل شوید و سپس دستور را اجرا کنید (به یاد داشته باشید `<Profilename>` را با نام پروفایل خود جایگزین کنید):

**در ویندوز:**
```powershell
cd "$env:LOCALAPPDATA\hermes\profiles\<Profilename>\skills\hesabfa-accounting"
python .\scripts\hesabfa.py selftest
```

**در لینوکس و مک:**
```bash
cd ~/.local/share/hermes/profiles/<Profilename>/skills/hesabfa-accounting
python3 ./scripts/hesabfa.py selftest
```

**خروجی موفق:**
```text
خودآزمایی موفق: تبدیل تاریخ شمسی (به وقت تهران)، queryInfo، ترجمه‌ی search به فلتر و enum ها سالم هستند.
```
اگر خطایی دیدید، نصب Python و مسیر اسکریپت را بررسی کنید.

---

## 💡 نحوه استفاده — مثال‌های کاربردی

*(برای اجرای دستورات زیر، باید در پوشه `hesabfa-accounting` باشید)*

### جستجوی مشتری
```bash
python3 scripts/hesabfa.py contact list --search "شرکت آریا" --take 50
```

### ثبت مشتری جدید
```bash
python3 scripts/hesabfa.py contact save \
  --name "شرکت آریا" \
  --company "شرکت آریا" \
  --contact-type حقوقی \
  --is-customer \
  --mobile "09121234567" \
  --node-family "اشخاص:اشخاص حقوقی:مشتریان"
```

### صدور فاکتور فروش
ابتدا یک فایل به نام `invoice.json` با محتوای زیر بسازید:
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
سپس دستور زیر را اجرا کنید:
```bash
python3 scripts/hesabfa.py invoice save --file invoice.json
```

### دریافت گزارش تراز آزمایشی
```bash
python3 scripts/hesabfa.py report trial-balance --start-date "1404/01/01" --end-date "1404/08/29"
```

### تست Dry-run (بدون ارسال به سرور)
```bash
python3 scripts/hesabfa.py invoice save --file invoice.json --dry-run
```

---

## 📁 ساختار پوشه‌ها پس از نصب

```text
hesabfa-accounting/
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

## 🛡️ نکات امنیتی مهم

1. **هرگز** `apiKey` و `loginToken` را در فایل‌های پروژه قرار ندهید.
2. متغیرهای محیطی فقط در سشن ترمینال جاری معتبرند (برای دائمی شدن، باید در تنظیمات سیستم ست شوند).
3. خروجی `--dry-run` کلیدها را ماسک می‌کند (`abcd…`) — خیالتان راحت باشد.
4. قبل از هر دستور `delete` از کاربر تأیید صریح بگیرید.

---

## 🔧 رفع اشکال (Troubleshooting)

| مشکل | راه‌حل |
|------|--------|
| `exit code 4: apiKey تنظیم نشده` | متغیر `HESABFA_API_KEY` را ست کنید |
| `exit code 2: HTTP 403` | loginToken منقضی شده — از پنل حسابفا جدید بگیرید |
| `exit code 101: TooManyRequests` | ۱۰ ثانیه صبر کنید (CLI خودش retry می‌کند) |
| `خطای 199: Invalid login token` | توکن را مجدداً از پنل حسابفا دریافت کنید |
| `SystemExit: فرمت تاریخ نامعتبر` | از فرمت `1404/08/23` یا `2025-11-14` استفاده کنید |

---

## 🔗 منابع

- مستندات رسمی حسابفا: [https://www.hesabfa.com/help/api](https://www.hesabfa.com/help/api)
- پروژه GitHub: [https://github.com/msnwoodpainting/hesabfa-accounting](https://github.com/msnwoodpainting/hesabfa-accounting)