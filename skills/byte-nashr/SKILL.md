---
name: byte-nashr
description: Publishes a byte-virastari-corrected Persian article (text + loose images) into the byte-new-website repo as a schema-valid MDX article — writes frontmatter, resolves/creates authors, optimizes images (SVG passthrough, WebP conversion over 300KB), converts {{term|explanation}} footnote markers into Tooltip components, and validates against the site's content schema. Use when asked to add/publish an edited Byte article to the website.
---

# انتشار بایت

این اسکیل خروجی `byte-virastari` (متن ویراستاری‌شده + عکس‌های آزاد) را
به یک مقالهٔ کامل و معتبر (طبق اسکیمای محتوا) در ریپوی `byte-new-website`
تبدیل می‌کند.

**کِی فعال شو:** وقتی کاربر یک متن ویراستاری‌شده دارد (معمولاً خروجی
`byte-virastari`) و می‌خواهد آن را به‌عنوان مقالهٔ جدید در وب‌سایت بایت
اضافه کند.

## مرجع‌ها

- `references/schema-va-mahal.md` — اسکیمای frontmatter مقاله و قرارداد
  محل قرارگیری فایل‌ها/اسلاگ.
- `references/tasavir.md` — سیاست بهینه‌سازی تصاویر (SVG بدون تغییر،
  تبدیل به WebP برای فایل‌های بزرگ‌تر از ۳۰۰ کیلوبایت).
- `references/nevisandegan.md` — روند تشخیص/تأیید/ساخت نویسنده.

## مسیر ریپوی وب‌سایت

پیش‌فرض این جلسه: `/Users/moeein/Documents/byte/byte-new-website`. اگر
این مسیر وجود نداشت یا کاربر مسیر دیگری داد، از همان استفاده کن؛ در
صورت ابهام از کاربر بپرس.

## گردش‌کار

1. **خواندن پوشهٔ ورودی.** متن ویراستاری‌شده (معمولاً حاوی نشانه‌های
   `{{واژه|توضیح}}`) و تمام عکس‌های آزاد کنار آن را بخوان. پوشهٔ ورودی
   ساختار خاصی ندارد — خودت باید تشخیص بدهی کدام عکس کجای متن استفاده
   می‌شود (بر اساس نام فایل، ترتیب در متن، یا ارجاع صریح نویسنده به
   تصویر).

2. **نگارش frontmatter.** طبق `references/schema-va-mahal.md`:
   - `title`, `description` را از متن استخراج کن.
   - `tags` را با توجه به موضوع مقاله پیشنهاد بده (و در صورت ابهام از
     کاربر بپرس).
   - `date` را از کاربر بپرس اگر مشخص نیست (پیش‌فرض تاریخ امروز
     پذیرفتنی نیست بدون تأیید).
   - `issue` را **همیشه** از کاربر بپرس — هرگز حدس نزن؛ و بررسی کن
     پوشهٔ آن issue از قبل در `content/issues/` وجود دارد.
   - `order` را با نگاه‌کردن به بقیهٔ مقالات همان issue تعیین کن.

3. **تعیین اسلاگ و مسیر مقصد.** طبق `references/schema-va-mahal.md`
   اسلاگ لاتین kebab-case بساز و مسیر
   `content/issues/<issue>/<slug>/` را ایجاد کن.

4. **تحلیل و تأیید نویسنده(ها).** طبق `references/nevisandegan.md` —
   **همیشه** نتیجهٔ جست‌وجو (مطابقت پیدا شد یا نشد) را به کاربر نشان
   بده و تأیید بگیر، چه برای استفادهٔ دوباره از شناسهٔ موجود، چه برای
   ساخت نویسندهٔ جدید. هرگز خودسرانه تصمیم نگیر.

5. **پردازش تصاویر.** طبق `references/tasavir.md` — SVGها بدون تغییر،
   رستری‌های بزرگ‌تر از ۳۰۰ کیلوبایت با `cwebp` به WebP تبدیل شوند،
   ارجاعات داخل متن به‌روزرسانی شوند، فایل‌ها در `img/` مقالهٔ جدید (و
   عکس نویسنده در صورت وجود در `public/img/authors/`) قرار بگیرند.

6. **تبدیل پاورقی به Tooltip.** هر نشانهٔ `{{واژه یا عبارت|توضیح}}` در
   متن را به این JSX تبدیل کن:

   ```jsx
   <Tooltip tip="توضیح"><span>واژه یا عبارت</span></Tooltip>
   ```

   دقیقاً مطابق الگوی استفادهٔ موجود در `components/content/tooltip.tsx`
   و مقالات چاپ‌شده.

7. **اعتبارسنجی.** داخل ریپوی `byte-new-website` اجرا کن:

   ```bash
   pnpm typecheck
   pnpm test
   ```

   اگر خطایی گزارش شد (مثلاً frontmatter نامعتبر طبق
   `lib/content/schema.test.ts`)، آن را برای کاربر توضیح بده و اصلاح
   کن؛ دوباره اعتبارسنجی را اجرا کن تا پاک شود.

## کارهایی که این اسکیل هرگز انجام نمی‌دهد

- **commit یا push نمی‌کند** — تغییرات را در working tree می‌گذارد تا
  کاربر خودش مرور و commit کند.
- **`pnpm build` یا `pnpm prebuild` اجرا نمی‌کند** — این دستورها
  `public/` را تغییر می‌دهند و OG image تولید می‌کنند؛ این خارج از
  مسئولیت این اسکیل است.
- `content/issues/<issue>/meta.json` را نمی‌سازد یا ویرایش نمی‌کند.
- به `content/data/staff.ts` چیزی اضافه نمی‌کند مگر کاربر صراحتاً
  بخواهد.

## خلاصهٔ پایانی

در پایان، به کاربر خلاصه‌ای بده: مسیر فایل `index.mdx` ساخته‌شده، لیست
عکس‌های پردازش‌شده (با حجم قبل/بعد)، نویسنده(هایی) که استفاده/ساخته
شدند، تعداد پاورقی‌های تبدیل‌شده به Tooltip، و نتیجهٔ اعتبارسنجی.
