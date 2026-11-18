# حسابفا (Hesabfa) — API حسابداری ابری

## معرفی

**hesabfa-accounting** اسکیلی است برای اتصال هوش مصنوعی به نرم‌افزار حسابداری آنلاین حسابفا. با این اسکریپت CLI می‌توان تمام عملیات حسابداری از طریق API رسمی حسابفا (۹۲ متد در ۱۲ ماژول) انجام داد.

## امکانات

- ✅ مدیریت اشخاص (مشتری، تأمین‌کننده، کارمند)
- ✅ مدیریت کالا و خدمات
- ✅ صدور و مدیریت فاکتور فروش، خرید، برگشتی، ضایعات
- ✅ ثبت دریافت و پرداخت (رسید، چک، خرج چک)
- ✅ ثبت سند حسابداری دستی
- ✅ حواله و رسید انبار
- ✅ اقلام تخفیف‌دار و انتقال بین بانکی
- ✅ تنظیمات کسب‌وکار (سال مالی، بانک‌ها، صندوق‌ها، حساب‌ها، پروژه‌ها)
- ✅ گزارش‌های مالی کامل (ترازنامه، تراز آزمایشی، سود و زیان، دفتر روزنامه، گردش بانک/صندوق، بدهکاران و بستانکاران)
- ✅ سیستم استعلام (کد ملی، شبا، کارت، کد پستی)
- ✅ تبدیل خودکار تاریخ شمسی به میلادی
- ✅ ورودی enum ها با نام فارسی/انگلیسی
- ✅ پشتیبانی از `--dry-run` برای پیش‌نمایش بدون ارسال
- ✅ محدودیت نرخ ۱۲۰ درخواست/دقیقه با تلاش مجدد خودکار
- ✅ کلید idempotency (requestUniqueId)
- ✅ فیلترهای پیشرفته queryInfo

---

## 🚀 نصب و راه‌اندازی

برای نصب این اسکیل روی سیستم‌عامل‌های **ویندوز، مک و لینوکس** و اتصال آن به **Hermes Agent**، لطفاً راهنمای گام‌به‌گام زیر را مطالعه کنید:

👉 **[مشاهده راهنمای کامل نصب و استفاده (INSTALL_GUIDE_FA.md)](./INSTALL_GUIDE_FA.md)**

*(در این راهنما، دستورات نصب پیش‌نیازها، دانلود فایل زیپ، نحوه استخراج در پوشه هرمس و تنظیم کلیدهای API به طور کامل توضیح داده شده است).*

---

## ساختار پروژه

```text
hesabfa-accounting/
├── SKILL.md                    # راهنمای اسکیل (برای Hermes Agent)
├── README.md                   # این فایل
├── INSTALL_GUIDE_FA.md         # راهنمای کامل نصب
├── CHANGELOG.md                # تاریخچه تغییرات
├── assets/                     # تصاویر، لوگو و…
├── scripts/
│   └── hesabfa.py              # رابط CLI اصلی (۱۳۳۵ خط، فقط stdlib)
├── references/
│   ├── api-reference.md        # فهرست ۹۲ متد API
│   ├── enums-errors.md         # جداول enum و کدهای خطا
│   ├── objects.md              # ساختار اشیای JSON
│   └── recipes.md              # گردش‌کارهای رایج
└── tests/
    └── test_hesabfa.py         # تست‌های خودکار آفلاین
```

## نمونه استفاده (پیشرفته)

### ثبت مشتری جدید
```bash
python3 scripts/hesabfa.py contact save \
  --name "شرکت توحید" \
  --company "شرکت توحید" \
  --contact-type حقوقی \
  --is-customer \
  --mobile 09361234567 \
  --node-family "اشخاص:اشخاص حقوقی:مشتریان" \
  --api-key $HESABFA_API_KEY \
  --login-token $HESABFA_LOGIN_TOKEN
```

### صدور فاکتور فروش
```bash
python3 scripts/hesabfa.py invoice save \
  --date "1404/08/23" \
  --due-date "1404/09/23" \
  --contact-code "0001" \
  --invoice-type فروش \
  --status 1 \
  --items-json '[{"itemCode":"K001","quantity":2,"unitPrice":950000,"discount":100000,"tax":90000}]' \
  --api-key $HESABFA_API_KEY \
  --login-token $HESABFA_LOGIN_TOKEN
```

### دریافت گزارش تراز آزمایشی
```bash
python3 scripts/hesabfa.py report trial-balance \
  --start-date "1404/01/01" \
  --end-date "1404/08/29" \
  --api-key $HESABFA_API_KEY \
  --login-token $HESABFA_LOGIN_TOKEN \
  --result
```

### تست خودکار (آفلاین)
```bash
python3 scripts/hesabfa.py selftest
```

---

## مستندات

- [`references/api-reference.md`](references/api-reference.md) — فهرست کامل ۹۲ متد
- [`references/objects.md`](references/objects.md) — ساختار اشیای JSON
- [`references/enums-errors.md`](references/enums-errors.md) — جداول enum و کدهای خطا
- [`references/recipes.md`](references/recipes.md) — گردش‌کارهای رایج
- [مستندات رسمی حسابفا](https://www.hesabfa.com/help/api)

## کدهای خروج

| کد | معنی |
|---|---|
| `0` | موفق (`Success: true`) |
| `2` | خطای شبکه/HTTP |
| `3` | خطای منطقی API (`Success: false`) |
| `4` | خطای استفاده/پارامتر |

## لایسنس

MIT — استفاده آزاد برای پروژه‌های شخصی و تجاری.

## مشارکت

لطفاً issue و pull request بفرستید. برای تست محلی:
```bash
python3 scripts/hesabfa.py selftest
python3 -m pytest tests/
```