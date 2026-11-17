<div dir="rtl" align="right">

## معرفی پوشه

نمونه‌برنامه‌ها و پروژه‌های آزمایشی برای میکروکنترلر CH32V305 با استفاده از کتابخانه ch32-hal.

</div>

## Folder Overview

Example applications and test projects for the CH32V305 microcontroller using ch32-hal.

---

# ch32v305-examples

## Boards

### My Custom CH32V305 Board

## Target

[`riscv32imfc-unknown-none-elf.json`](riscv32imfc-unknown-none-elf.json) enables `xwchc`, WCH's compressed byte and halfword loads and stores. QingKe V4F implements the extension (QingKe V3 manual, table 1-1).

<div dir="rtl" align="right">

## معرفی و راهنمای نمونه‌برنامه‌های CH32V305

این پوشه برای نمونه‌برنامه‌ها و پروژه‌های آزمایشی میکروکنترلر **CH32V305** با استفاده از کتابخانه `ch32-hal` در نظر گرفته شده است.

## برد سخت‌افزاری

### My Custom CH32V305 Board

ساختار فعلی پوشه یک برد سفارشی با عنوان `My Custom CH32V305 Board` را معرفی می‌کند. جزئیات اتصالات، پایه LED و تجهیزات جانبی این برد در README مشخص نشده است؛ بنابراین هنگام اجرای نمونه‌ها باید تخصیص پایه‌ها را از شماتیک و طراحی سخت‌افزار خود استخراج و در برنامه اعمال کنید.

## هدف کامپایل و معماری پردازنده

فایل هدف کامپایل پروژه [`riscv32imfc-unknown-none-elf.json`](riscv32imfc-unknown-none-elf.json) است. این فایل تنظیمات معماری هدف را برای ساخت برنامه bare-metal مشخص می‌کند و افزونه `xwchc` را فعال می‌سازد.

افزونه `xwchc` شامل دستورهای فشرده اختصاصی WCH برای بارگذاری و ذخیره‌سازی بایت و نیم‌کلمه است. هسته `QingKe V4F` از این افزونه پشتیبانی می‌کند. برای اطلاعات بیشتر درباره قابلیت‌های هسته و این افزونه به جدول ۱-۱ راهنمای هسته `QingKe V3` ارجاع داده شده است.

## نکات راه‌اندازی

- فایل JSON هدف کامپایل باید با معماری پردازنده انتخاب‌شده سازگار باشد.
- پایه‌های GPIO، کلاک‌ها و تجهیزات جانبی را بر اساس شماتیک برد سفارشی تنظیم کنید.
- پیش از استفاده از نمونه‌های آماده، بررسی کنید که پایه‌های تعریف‌شده در کد با اتصالات واقعی برد یکسان باشند.

</div>


---

## تصاویر و دیاگرام‌های راهنما

<p align="center"><img src="../../Images/GitHub-Assets/MES-Section-Header.svg" width="70%" alt="MES Section Header"></p>

## تهیه و تدوین

**معمار الکترونیک صنعت | MEMAR ELECTRONIC SANAT (MES)**  
همراه شما در مسیر طراحی و ساخت مدارهای مطمئن

- [Telegram](https://t.me/Memar_Electronic)
- [Instagram](https://www.instagram.com/Memar.Electronic)
- [مشاهده سایر آموزش‌ها و پروژه‌ها در GitHub](https://github.com/MEMAR-ELECTRONIC-SANAT)

<p align="center"><img src="../../Images/GitHub-Assets/MES_Animated_Logo_640x480.svg" width="140" alt="MES Animated Logo"></p>
