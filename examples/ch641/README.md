<div dir="rtl" align="right">

## معرفی پوشه

این پوشه شامل نمونه‌برنامه‌ها و پروژه‌های آزمایشی برای میکروکنترلر **CH641** بر پایه کتابخانه **ch32-hal** است.

## نکات فنی CH641

- قابلیت شارژ بی‌سیم رسمی این تراشه متن‌باز نیست و کد منبع آن منتشر نشده است.
- مطابق دیتاشیت، پایه **ISP** با نام **PB6** نیز شناخته می‌شود؛ برای این پایه هیچ عملکرد GPIO تعریف نشده است. بنابراین نباید آن را به‌عنوان GPIO عمومی در نظر گرفت.

## بردهای پشتیبانی‌شده

### CH641P-R0-1v0

برد رسمی ارزیابی و توسعه (EVT) شرکت سازنده است.

### Dino-Board

برد توسعه CH641 که برای سازگاری با برد رسمی **CH641P-R0-1v0** طراحی شده است.

- صفحه پروژه: [Dino-Board در OSHWHub](https://oshwhub.com/andelf/dino-board)
- **LED1:** پایه PB1
- **LED2:** پایه PA6
- **CH1P:** پایه PB3؛ عملکرد جایگزین TIM1_CH2
- **CH1N:** پایه PA2؛ عملکردهای جایگزین TIM1_CH1N و TIM1_CH3
- **CH2P:** پایه PB4؛ عملکردهای جایگزین TIM1_CH3، TIM1_CH1N و TIM2_CH2N
- **CH2N:** پایه PA4؛ عملکردهای جایگزین TIM1_CH3N و TIM2_CH2

پسوندهای P و N سیگنال‌های متناظر هر کانال را مشخص می‌کنند. عملکردهای تایمر، قابلیت‌های جایگزین پایه‌ها هستند و انتخاب نهایی آن‌ها به پیکربندی مالتی‌پلکس پایه وابسته است.

## هدف کامپایل و معماری پردازنده

فایل هدف کامپایل [riscv32ec-unknown-none-elf.json](riscv32ec-unknown-none-elf.json) افزونه **xwchc** را فعال می‌کند. این افزونه دستورهای فشرده بارگذاری و ذخیره‌سازی داده‌های بایت و نیم‌کلمه را فراهم می‌کند.

هسته **QingKe V2A** این افزونه را پیاده‌سازی می‌کند. برای اطلاعات تکمیلی به جدول ۱-۱ راهنمای هسته **QingKe V3** مراجعه کنید.

</div>

## Folder Overview

Example applications and test projects for the CH641 microcontroller using ch32-hal.

---

# ch641-examples

Note that the official wireless charging functionality is not open source.

According to the datasheet, ISP pin is also named PB6. No GPIO function is assigned to this pin.

## Boards

### CH641P-R0-1v0

Official EVT

### Dino-Board

Dino-Board is a development board for CH641. It is designed to be compatible with the official CH641P-R0-1v0 board.

Link: <https://oshwhub.com/andelf/dino-board>

- LED1: PB1
- LED2: PA6
- CH1P: PB3, TIM1_CH2
- CH1N: PA2, TIM1_CH1N, TIM1_CH3
- CH2P: PB4, TIM1_CH3, TIM1_CH1N, TIM2_CH2N
- CH2N: PA4, TIM1_CH3N, TIM2_CH2

## Target

[riscv32ec-unknown-none-elf.json](riscv32ec-unknown-none-elf.json) enables xwchc, WCH's compressed byte and halfword loads and stores. QingKe V2A implements the extension (QingKe V3 manual, table 1-1).

---

## تصاویر و دیاگرام‌های راهنما

<p align="center"><img src="../../Images/GitHub-Assets/MES-Section-Header.svg" width="70%" alt="MES Section Header"></p>

## تهیه و تدوین

<div dir="rtl" align="right">

**معمار الکترونیک صنعت | MEMAR ELECTRONIC SANAT (MES)**  
همراه شما در مسیر طراحی و ساخت مدارهای مطمئن

- [Telegram](https://t.me/Memar_Electronic)
- [Instagram](https://www.instagram.com/Memar.Electronic)
- [مشاهده سایر آموزش‌ها و پروژه‌ها در GitHub](https://github.com/MEMAR-ELECTRONIC-SANAT)

</div>

<div dir="ltr" align="left">

**Prepared and Maintained By**  
MEMAR ELECTRONIC SANAT (MES)  
Reliable circuit design and development, alongside you.

- [Telegram](https://t.me/Memar_Electronic)
- [Instagram](https://www.instagram.com/Memar.Electronic)
- [Explore more tutorials and projects on GitHub](https://github.com/MEMAR-ELECTRONIC-SANAT)

</div>

<p align="center"><img src="../../Images/GitHub-Assets/MES_Animated_Logo_640x480.svg" width="180" alt="MES Animated Logo"></p>
