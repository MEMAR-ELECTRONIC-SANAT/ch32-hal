# src

<div dir="rtl" align="right">

## معرفی پوشه

ماژول اصلی کتابخانه HAL؛ شامل درایورهای سخت‌افزاری، مدیریت Peripheralها و رابط‌های مشترک.

## محتویات

`adc/`، `can/`، `dac/`، `delay/`، `dma/`، `embassy/`، `eth/`، `flash/`، `i2c/`، `i3c/`، `internal/`، `otg_fs/`، `rcc/`، `sdio/`، `timer/`، `usart/`، `usb/`، `usbfs/`، `usbhs/`، `usbpd/`، `usbss/`، `wdg/`، `adc.rs`، `awu.rs`، `crc.rs`، `dac.rs`، `debug.rs`، `exti.rs`، `gpio.rs`، `i2c.rs`، `i3c.rs`، `interrupt_ext.rs`، `lib.rs`، `macros.rs`

## نکات استفاده

- سازگاری کدها را با مدل دقیق تراشه و Featureهای فعال‌شده در `Cargo.toml` بررسی کنید.
- وضعیت پیاده‌سازی و اعتبارسنجی Peripheralها ممکن است بین مدل‌ها متفاوت باشد.
- برای معرفی کلی پروژه به [README اصلی مخزن](../README.md) مراجعه کنید.

</div>

---

## Folder Overview

Core HAL source tree containing peripheral drivers and shared hardware abstractions.

## Contents

`adc/`, `can/`, `dac/`, `delay/`, `dma/`, `embassy/`, `eth/`, `flash/`, `i2c/`, `i3c/`, `internal/`, `otg_fs/`, `rcc/`, `sdio/`, `timer/`, `usart/`, `usb/`, `usbfs/`, `usbhs/`, `usbpd/`, `usbss/`, `wdg/`, `adc.rs`, `awu.rs`, `crc.rs`, `dac.rs`, `debug.rs`, `exti.rs`, `gpio.rs`, `i2c.rs`, `i3c.rs`, `interrupt_ext.rs`, `lib.rs`, `macros.rs`

## Usage Notes

- Check compatibility with the exact MCU model and enabled Cargo features.
- Peripheral implementation and validation status may differ between devices.
- See the [repository root README](../README.md) for project-wide guidance.


---

## تصاویر و دیاگرام‌های راهنما

<p align="center"><img src="../Images/GitHub-Assets/MES-Section-Header.svg" width="70%" alt="MES Section Header"></p>

## تهیه و تدوین

**معمار الکترونیک صنعت | MEMAR ELECTRONIC SANAT (MES)**  
همراه شما در مسیر طراحی و ساخت مدارهای مطمئن

- [Telegram](https://t.me/Memar_Electronic)
- [Instagram](https://www.instagram.com/Memar.Electronic)
- [مشاهده سایر آموزش‌ها و پروژه‌ها در GitHub](https://github.com/MEMAR-ELECTRONIC-SANAT)

<p align="center"><img src="../Images/GitHub-Assets/MES_Animated_Logo_640x480.svg" width="140" alt="MES Animated Logo"></p>
