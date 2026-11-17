<div dir="rtl" align="right">

## معرفی پوشه

نمونه‌برنامه‌ها و پروژه‌های آزمایشی برای میکروکنترلر CH32X035 با استفاده از کتابخانه ch32-hal.

</div>

<div dir="rtl" align="right">

## معرفی پوشه

این پوشه شامل نمونه‌برنامه‌ها و پروژه‌های آزمایشی برای میکروکنترلر **CH32X035** است که با استفاده از کتابخانه **ch32-hal** توسعه داده شده‌اند.

# CH32X035

در نمونه‌های این پوشه، کلید کاربر به پایه **PC3** متصل است و با اتصال به سطح منطقی پایین (Active Low) فعال می‌شود. این پایه در وضعیت پایین نگه داشته شده یا ممکن است شناور (Floating) باشد؛ بنابراین هنگام استفاده از نمونه‌ها، وضعیت اتصال و مدار Pull مربوط به کلید را مطابق سخت‌افزار خود بررسی کنید.

## نمونه‌برنامه‌ها

جدول زیر کاربرد هر یک از نمونه‌برنامه‌های موجود را نشان می‌دهد:

| فایل / پوشه | عملکرد و موضوع آموزشی |
|---|---|
| `awu` | کار با تایمر بیدارباش خودکار (Auto-Wakeup)، در دو حالت مسدودکننده (Blocking) و ناهمگام (Async) |
| `blinky` | راه‌اندازی خروجی GPIO و چشمک‌زدن LED کاربر |
| `blinky_pwm` | تولید سیگنال PWM با استفاده از تایمر و اعمال آن به پایه LED |
| `exti` | راه‌اندازی وقفه خارجی (External Interrupt) برای کلید |
| `flash` | خواندن و نوشتن ناحیه Option Bytes حافظه Flash |
| `i2c_oled` | راه‌اندازی نمایشگر OLED مبتنی بر SSD1306 از طریق رابط I2C |
| `sdi_print` | ارسال خروجی تابع `println!` از طریق رابط دیباگ تک‌سیمه (Single-Wire Debug Interface) |
| `spi-lcd-st7735` | راه‌اندازی نمایشگر مبتنی بر ST7735 از طریق رابط SPI |
| `uart_echo` | دریافت و بازفرست داده از طریق USART4 (UART Echo) |
| `uart_pms7003_async` | دریافت داده از USART4 با استفاده از DMA در حالت ناهمگام |
| `pioc_rainbow` | کنترل پنل LEDهای WS2812 از طریق واحد PIOC و پایه `PC18` |

## نکات فنی نمونه PIOC و WS2812

نمونه `pioc_rainbow` برای اجرا به قابلیت (Feature) دستی `pioc` در HAL نیاز دارد. این قابلیت از قبل در تنظیمات crate فعال شده است. همچنین باید تصویر Firmware مربوط به PIOC در ناحیه SRAM اختصاص‌یافته به برنامه PIOC، در نشانی `0x2000_4000` قرار بگیرد.

دو نکته مهم در پیکربندی حافظه و کلاک این نمونه وجود دارد:

1. **جایگاه Stack در حافظه:** فایل پیکربندی `memory.x` پشته (Stack) را در بالای این بازه حافظه قرار می‌دهد. به همین دلیل، فایل لینکِر `pioc.x` مقدار `_stack_start` را به پایین این ناحیه منتقل می‌کند. این قطعه تنظیمات لینکِر فقط به همین باینری اختصاص دارد و نباید به سایر نمونه‌ها تعمیم داده شود.

2. **فرکانس کلاک و زمان‌بندی WS2812:** پیکربندی پیش‌فرض `Config::default()`، کلاک HSI را بر عدد شش تقسیم می‌کند. ازاین‌رو این نمونه به‌صورت صریح از `SYSCLK_FREQ_48MHZ_HSI` استفاده می‌کند. تصویر برنامه PIOC نیز برای تولید زمان‌بندی بیت‌ها با فرکانس ۴۸ مگاهرتز آماده شده است؛ تغییر فرکانس سیستم بدون هماهنگ‌سازی تصویر PIOC می‌تواند زمان‌بندی سیگنال WS2812 را مختل کند.

## آزادسازی پایه‌های دیباگ و برنامه‌ریزی مجدد

با فعال‌کردن گزینه `release_swd_pins: true` در ساختار `Config`، پایه‌های RVSWD برای استفاده واحد PIOC آزاد می‌شوند. پس از آزادسازی این پایه‌ها، اتصال مجدد دیباگر امکان‌پذیر نخواهد بود.

برای برنامه‌ریزی مجدد تراشه، باید آن را از طریق Reset وارد **System Bootloader** کنید و سپس Firmware را دوباره بارگذاری نمایید. بنابراین پیش از فعال‌کردن این گزینه، محدودیت آن را در روند توسعه و عیب‌یابی در نظر بگیرید.

</div>

## Folder Overview

Example applications and test projects for the CH32X035 microcontroller using ch32-hal.

---

# CH32X035

Button PC3, active low. Pulled down or floating.

## Examples

| File | What it shows |
|---|---|
| `awu` | auto-wakeup timer, blocking and async |
| `blinky` | GPIO output on the user LED |
| `blinky_pwm` | timer PWM on an LED pin |
| `exti` | external interrupt on the button |
| `flash` | reading and writing the option bytes area |
| `i2c_oled` | SSD1306 over I2C |
| `sdi_print` | `println!` over the single-wire debug interface |
| `spi-lcd-st7735` | SPI display |
| `uart_echo` | USART4 echo |
| `uart_pms7003_async` | USART4 receive with DMA |
| `pioc_rainbow` | WS2812 panel through the PIOC, `PC18` |

`pioc_rainbow` needs the HAL's manual `pioc` feature, which the crate already
enables, and a firmware image in the PIOC program SRAM at `0x2000_4000`. Two
consequences the example handles:

- `memory-x` puts the stack on top of that window, so `pioc.x` moves
  `_stack_start` below it. Only that binary gets the fragment.
- `Config::default()` divides HSI by six, so the example asks for
  `SYSCLK_FREQ_48MHZ_HSI`; the image is assembled for a 48 MHz bit period.

`release_swd_pins: true` in the `Config` frees the RVSWD pins for the PIOC.
The debugger cannot attach afterwards, so reset the chip into its system
bootloader to program it again.

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
