<div dir="rtl" align="right">

## معرفی پوشه

نمونه‌برنامه‌ها و پروژه‌های آزمایشی برای میکروکنترلر CH32V003 با استفاده از کتابخانه ch32-hal.

</div>

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
