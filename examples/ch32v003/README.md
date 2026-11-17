<div dir="rtl" align="right">

## معرفی و راهنمای نمونه‌برنامه‌های CH32V003

این پوشه مجموعه‌ای از نمونه‌برنامه‌ها و پروژه‌های آزمایشی برای میکروکنترلر **CH32V003** است که با کتابخانه Rust به نام `ch32-hal` توسعه داده شده‌اند. اطلاعات بردهای پشتیبانی‌شده، تخصیص پایه‌ها و روش‌های کامپایل و بارگذاری برنامه در ادامه آمده است.

## بردهای پشتیبانی‌شده

### nanoCH32V003

- میکروکنترلر: `CH32V003F4U6`
- پایه LED: `PD6`
- صفحه و اطلاعات برد: [wuxx/nanoCH32V003](https://github.com/wuxx/nanoCH32V003)

### LineDog CH32V003J4M6

- صفحه معرفی و طراحی برد: [OSHWHUB – LineDog](https://oshwhub.com/andelf/linedog)
- میکروکنترلر: `CH32V003J4M6`
- پایه‌های LED: `PA2` و `PC4`
- این برد از کریستال خارجی پرسرعت (HSE) استفاده نمی‌کند.

### LineDog v2 CH32V003F4P6

- خروجی بازر: پایه `PC4` با عملکرد جایگزین `TIM1CH4`
- اتصال LEDهای آدرس‌پذیر: پایه `PA2` برای `WS2812`

توجه کنید تخصیص پایه‌ها و تجهیزات جانبی ممکن است بین نسخه‌های مختلف برد تفاوت داشته باشد؛ پیش از اتصال سخت‌افزار، شماتیک همان نسخه را بررسی کنید.

## آماده‌سازی محیط کامپایل

برای ساخت این پروژه باید از نسخه آزمایشی Rust یعنی `nightly` استفاده شود. ابتدا آن را نصب و برای پوشه پروژه فعال کنید:

```bash
rustup install nightly
rustup override set nightly
```

پیکربندی هدف کامپایل در فایل [`riscv32ec-unknown-none-elf.json`](riscv32ec-unknown-none-elf.json) قرار دارد. این مشخصات امکان کامپایل برای معماری `riscv32ec` را فراهم می‌کند و افزونه `xwchc` را نیز فعال می‌سازد. این افزونه دستورهای فشرده بارگذاری و ذخیره‌سازی بایت و نیم‌کلمه مخصوص WCH را فعال می‌کند. هسته `QingKe V2A` از این قابلیت پشتیبانی می‌کند؛ برای جزئیات به جدول ۱-۱ راهنمای هسته `QingKe V3` مراجعه کنید.

در صورت نیاز، کد منبع کتابخانه استاندارد `core` را نیز نصب کنید:

```bash
rustup component add rust-src
```

## ساخت و اجرای نمونه‌ها با wlink

ابزار [`wlink`](https://github.com/ch32-rs/wlink) یکی از روش‌های بارگذاری و اجرای برنامه روی میکروکنترلرهای WCH است.

1. ابزار را طبق [راهنمای نصب wlink](https://github.com/ch32-rs/wlink?tab=readme-ov-file#install) نصب کنید.
2. فایل [`.cargo/config.toml`](.cargo/config.toml) را باز کرده و مقدار runner را روی `wlink` قرار دهید. در برخی نسخه‌های پروژه این تنظیم از قبل به‌عنوان runner پیش‌فرض تعریف شده است.
3. نمونه چشمک‌زن LED با نام `blinky` را کامپایل و اجرا کنید:

```bash
cargo run --release --bin blinky
```

## ساخت و اجرای نمونه‌ها با probe-rs

روش دیگر استفاده از [`probe-rs`](https://probe.rs/) است.

1. ابزار را از [راهنمای رسمی نصب probe-rs](https://probe.rs/docs/getting-started/installation/) نصب کنید.
2. در سیستم‌عامل‌های مبتنی بر Linux ممکن است لازم باشد قوانین `udev` را اضافه کنید تا ابزار `probe-rs` اجازه دسترسی به دیباگر `WCH-Link` را داشته باشد. راهنما: [قوانین udev در Linux](https://probe.rs/docs/getting-started/probe-setup/#linux%3A-udev-rules).
3. فایل [`.cargo/config.toml`](.cargo/config.toml) را ویرایش کنید و runner را به شکل زیر تنظیم کنید:

```toml
runner = "probe-rs run --chip ch32v003"
```

4. نمونه `blinky` را بسازید و اجرا کنید:

```bash
cargo run --release --bin blinky
```

## نکات اجرایی

- برای ساخت پروژه از دستورهای داخل پوشه مخزن و با نسخه `nightly` استفاده کنید.
- روش بارگذاری انتخاب‌شده باید با مقدار runner در `.cargo/config.toml` هماهنگ باشد.
- برای نمونه `probe-rs`، شناسه تراشه باید `ch32v003` تعیین شود.
- پایه LED در هر برد یکسان نیست؛ برای نمونه، در nanoCH32V003 پایه `PD6` و در LineDog پایه‌های `PA2` و `PC4` معرفی شده‌اند.

</div>

---

## Folder Overview

Example applications and test projects for the CH32V003 microcontroller using ch32-hal.

---

# ch32v003-examples

## Boards

### nanoCH32V003

Link: [wuxx/nanoCH32V003](https://github.com/wuxx/nanoCH32V003)

- MCU: CH32V003F4U6
- LED: PD6

### LineDog CH32V003J4M6 Board

Link: [OSHWHUB](https://oshwhub.com/andelf/linedog)

- MCU: CH32V003J4M6
- LED: PA2, PC4
- No HSE crystal

### LineDog v2 CH32V003F4P6 Board

- BUZZ: PC4 (TIM1CH4)
- WS2812: PA2

## Running Examples

### using rust-nightly for building

call `rustup install nightly` and `rustup override set nightly` to build this project with rust nightly,
which allows to compilation for riscv32ec using the [json-file](riscv32ec-unknown-none-elf.json) provided.
That spec also enables `xwchc`, WCH's compressed byte and halfword loads and stores (QingKe V2A implements it; QingKe V3 manual, table 1-1).

You might need to install the sources for core with `rustup component add rust-src`

### Using [wlink](https://github.com/ch32-rs/wlink)
- Install wlink using the installation instructions: https://github.com/ch32-rs/wlink?tab=readme-ov-file#install

- Edit the [`.cargo/config.toml`](.cargo/config.toml) file so the runner is `wlink`. This may already be the default runner.

- Build and run the [blinky](src/bin/blinky.rs) example with `cargo run --release --bin blinky`.

### Using [probe-rs](https://probe.rs/)

- Install probe-rs using the installation instructions: https://probe.rs/docs/getting-started/installation/
    - If you are on a Linux based system, you may have to add udev rules to allow probe-rs access to the WCH-Link debugger. https://probe.rs/docs/getting-started/probe-setup/#linux%3A-udev-rules

- Edit the [`.cargo/config.toml`](.cargo/config.toml) file so the runner is `probe-rs run --chip ch32v003`.

- Build and run the [blinky](src/bin/blinky.rs) example with `cargo run --release --bin blinky`.

<div dir="rtl" align="right">

## معرفی و راهنمای نمونه‌برنامه‌های CH32V003

این پوشه مجموعه‌ای از نمونه‌برنامه‌ها و پروژه‌های آزمایشی برای میکروکنترلر **CH32V003** است که با کتابخانه Rust به نام `ch32-hal` توسعه داده شده‌اند. اطلاعات بردهای پشتیبانی‌شده، تخصیص پایه‌ها و روش‌های کامپایل و بارگذاری برنامه در ادامه آمده است.

## بردهای پشتیبانی‌شده

### nanoCH32V003

- میکروکنترلر: `CH32V003F4U6`
- پایه LED: `PD6`
- صفحه و اطلاعات برد: [wuxx/nanoCH32V003](https://github.com/wuxx/nanoCH32V003)

### LineDog CH32V003J4M6

- صفحه معرفی و طراحی برد: [OSHWHUB – LineDog](https://oshwhub.com/andelf/linedog)
- میکروکنترلر: `CH32V003J4M6`
- پایه‌های LED: `PA2` و `PC4`
- این برد از کریستال خارجی پرسرعت (HSE) استفاده نمی‌کند.

### LineDog v2 CH32V003F4P6

- خروجی بازر: پایه `PC4` با عملکرد جایگزین `TIM1CH4`
- اتصال LEDهای آدرس‌پذیر: پایه `PA2` برای `WS2812`

توجه کنید تخصیص پایه‌ها و تجهیزات جانبی ممکن است بین نسخه‌های مختلف برد تفاوت داشته باشد؛ پیش از اتصال سخت‌افزار، شماتیک همان نسخه را بررسی کنید.

## آماده‌سازی محیط کامپایل

برای ساخت این پروژه باید از نسخه آزمایشی Rust یعنی `nightly` استفاده شود. ابتدا آن را نصب و برای پوشه پروژه فعال کنید:

```bash
rustup install nightly
rustup override set nightly
```

پیکربندی هدف کامپایل در فایل [`riscv32ec-unknown-none-elf.json`](riscv32ec-unknown-none-elf.json) قرار دارد. این مشخصات امکان کامپایل برای معماری `riscv32ec` را فراهم می‌کند و افزونه `xwchc` را نیز فعال می‌سازد. این افزونه دستورهای فشرده بارگذاری و ذخیره‌سازی بایت و نیم‌کلمه مخصوص WCH را فعال می‌کند. هسته `QingKe V2A` از این قابلیت پشتیبانی می‌کند؛ برای جزئیات به جدول ۱-۱ راهنمای هسته `QingKe V3` مراجعه کنید.

در صورت نیاز، کد منبع کتابخانه استاندارد `core` را نیز نصب کنید:

```bash
rustup component add rust-src
```

## ساخت و اجرای نمونه‌ها با wlink

ابزار [`wlink`](https://github.com/ch32-rs/wlink) یکی از روش‌های بارگذاری و اجرای برنامه روی میکروکنترلرهای WCH است.

1. ابزار را طبق [راهنمای نصب wlink](https://github.com/ch32-rs/wlink?tab=readme-ov-file#install) نصب کنید.
2. فایل [`.cargo/config.toml`](.cargo/config.toml) را باز کرده و مقدار runner را روی `wlink` قرار دهید. در برخی نسخه‌های پروژه این تنظیم از قبل به‌عنوان runner پیش‌فرض تعریف شده است.
3. نمونه چشمک‌زن LED با نام `blinky` را کامپایل و اجرا کنید:

```bash
cargo run --release --bin blinky
```

## ساخت و اجرای نمونه‌ها با probe-rs

روش دیگر استفاده از [`probe-rs`](https://probe.rs/) است.

1. ابزار را از [راهنمای رسمی نصب probe-rs](https://probe.rs/docs/getting-started/installation/) نصب کنید.
2. در سیستم‌عامل‌های مبتنی بر Linux ممکن است لازم باشد قوانین `udev` را اضافه کنید تا ابزار `probe-rs` اجازه دسترسی به دیباگر `WCH-Link` را داشته باشد. راهنما: [قوانین udev در Linux](https://probe.rs/docs/getting-started/probe-setup/#linux%3A-udev-rules).
3. فایل [`.cargo/config.toml`](.cargo/config.toml) را ویرایش کنید و runner را به شکل زیر تنظیم کنید:

```toml
runner = "probe-rs run --chip ch32v003"
```

4. نمونه `blinky` را بسازید و اجرا کنید:

```bash
cargo run --release --bin blinky
```

## نکات اجرایی

- برای ساخت پروژه از دستورهای داخل پوشه مخزن و با نسخه `nightly` استفاده کنید.
- روش بارگذاری انتخاب‌شده باید با مقدار runner در `.cargo/config.toml` هماهنگ باشد.
- برای نمونه `probe-rs`، شناسه تراشه باید `ch32v003` تعیین شود.
- پایه LED در هر برد یکسان نیست؛ برای نمونه، در nanoCH32V003 پایه `PD6` و در LineDog پایه‌های `PA2` و `PC4` معرفی شده‌اند.

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

<p align="center"><img src="../../Images/GitHub-Assets/MES_Animated_Logo_640x480.svg" width="180" alt="MES Animated Logo"></p>
