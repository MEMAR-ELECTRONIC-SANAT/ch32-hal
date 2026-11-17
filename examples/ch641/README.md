<div dir="rtl" align="right">

## معرفی پوشه

نمونه‌برنامه‌ها و پروژه‌های آزمایشی برای میکروکنترلر CH641 با استفاده از کتابخانه ch32-hal.

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

[`riscv32ec-unknown-none-elf.json`](riscv32ec-unknown-none-elf.json) enables `xwchc`, WCH's compressed byte and halfword loads and stores. QingKe V2A implements the extension (QingKe V3 manual, table 1-1).

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
