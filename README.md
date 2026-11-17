<div dir="rtl" align="right">

<p align="center"><img src="Images/GitHub-Assets/MES-Hero-Banner.svg" width="100%" alt="معمار الکترونیک صنعت - MES"></p>

<p align="center"><img src="Images/GitHub-Assets/MES-Divider.svg" width="100%" alt="جداکننده MES"></p>

# کتابخانه HAL برای میکروکنترلرهای ۳۲ بیتی WCH | ch32-hal

<p align="center"><img src="Images/GitHub-Assets/MES-Section-Header.svg" width="100%" alt="عنوان بخش MES"></p>

## معرفی

مخزن **ch32-hal** یک کتابخانه **Hardware Abstraction Layer (HAL)** مبتنی بر زبان **Rust** برای میکروکنترلرهای ۳۲ بیتی **RISC-V** شرکت **WCH** است. این HAL با چارچوب ناهمگام [Embassy](https://github.com/embassy-rs/embassy) یکپارچه شده و دسترسی یکپارچه‌ای به واحدهای جانبی خانواده‌های مختلف CH32 ارائه می‌کند.

این پروژه از روش **metapac** برای پشتیبانی چندین تراشه در یک Crate استفاده می‌کند. اطلاعات رجیسترها و قابلیت‌های تراشه‌ها در پروژه [ch32-data](https://github.com/ch32-rs/ch32-data) نگهداری می‌شود و از طریق Crate با نام `ch32-metapac` در دسترس است.

> **وضعیت توسعه:** این پروژه در حال توسعه است. اگرچه برای آزمایش و توسعه Firmware قابل استفاده است، ممکن است هنوز برای همه کاربردهای تولیدی پایدار نباشد. وضعیت هر Peripheral و میزان اعتبارسنجی آن را در نسخه جاری بررسی کنید.

<p align="center"><img src="Images/GitHub-Assets/MES-Status-Strip.svg" width="100%" alt="وضعیت و منابع پروژه MES"></p>

## ویژگی‌های اصلی

- پشتیبانی از چارچوب **Embassy** و برنامه‌نویسی ناهمگام مبتنی بر `async/await`.
- پشتیبانی یکپارچه از چندین خانواده میکروکنترلر در یک HAL.
- دسترسی به رجیسترهای Peripheral از طریق `ch32-metapac`.
- درایورهای Blocking و Async و پشتیبانی از DMA در واحدهای جانبی منتخب.
- امکان استفاده از ساختار نرم‌افزاری مشترک برای تراشه‌های پشتیبانی‌شده، با رعایت تفاوت‌های سخت‌افزاری هر مدل.

## خانواده‌های تحت پوشش

این مخزن خانواده‌هایی از جمله **CH32V2/V3، CH32V00x، CH32X0، CH32L1، CH641، CH643** و خانواده **CH32H4** را پوشش می‌دهد. خانواده CH32H4 شامل میکروکنترلر دو‌هسته‌ای **CH32H417** با هسته‌های **QingKe V3F و V5F** است.

انتخاب تراشه از طریق Featureهای تعریف‌شده در فایل [Cargo.toml](Cargo.toml) انجام می‌شود. فهرست دقیق مدل‌ها، وضعیت پشتیبانی و قابلیت‌های هر Peripheral ممکن است در نسخه‌های مختلف تغییر کند؛ برای جزئیات به [ch32-data](https://github.com/ch32-rs/ch32-data) و جدول انگلیسی پایین صفحه مراجعه کنید.

## واحدهای جانبی و درایورها

درایورها، بسته به خانواده و مدل تراشه، شامل موارد زیر هستند:

- **RCC و Clock Configuration**
- **GPIO، EXTI و PIOC**
- **UART، SPI و I2C**
- **ADC، Timer و PWM**
- **DMA و DMAMUX**
- **IWDG و WWDG**
- **USB Device، USB OTG FS، USB HS و USB SS**
- **Ethernet و CAN** در مدل‌های پشتیبانی‌شده
- درایورهای زمان و همگام‌سازی مورد نیاز **Embassy**

وجود یک درایور در کد منبع به معنی پشتیبانی کامل یا اعتبارسنجی آن روی تمام تراشه‌ها نیست. علائم جدول قابلیت‌ها وضعیت مورد انتظار، پیاده‌سازی‌نشده یا آزمایش‌نشده را مشخص می‌کنند.

## نکته مهم درباره CH32H417 دو‌هسته‌ای

نمونه‌های دو‌هسته‌ای **CH32H417** در مسیر [examples/ch32h417](examples/ch32h417/) قرار دارند. این نمونه‌ها از دو Image مستقل برای هسته‌های **V3F** و **V5F** استفاده می‌کنند و ارتباط بین هسته‌ها از طریق Mailbox حافظه مشترک انجام می‌شود. ابزار `xtask` نیز برای پروگرام و گزارش‌گیری نمونه‌ها در نظر گرفته شده است.

## نکته مهم: تنظیم تقسیم ROM/RAM در CH32V2/V3

در خانواده‌های CH32V2 و CH32V3، تقسیم حافظه ROM/RAM قابل تنظیم است و مقدار آن در بیت `FLASH_OBR.RAM_CODE_MOD` نگهداری می‌شود. اگر برنامه بلافاصله پس از پروگرام متوقف شود یا خطای دسترسی حافظه مانند `Store/AMO access fault` رخ دهد، ممکن است تنظیم تقسیم حافظه تراشه با Linker Script هم‌خوانی نداشته باشد.

برای بررسی و اصلاح این وضعیت، تنظیمات حافظه مورد انتظار را در تعریف تراشه در [ch32-data](https://github.com/ch32-rs/ch32-data) بررسی و در صورت نیاز با ابزار رسمی **WCHISPTool** پیکربندی کنید.

## شروع کار و انتخاب تراشه

1. مدل دقیق میکروکنترلر را مشخص کنید.
2. Feature متناظر با همان تراشه را در `Cargo.toml` فعال کنید.
3. Target مناسب معماری QingKe/RISC-V و تنظیمات حافظه را انتخاب کنید.
4. وضعیت Peripheralهای مورد نیاز را در جدول پشتیبانی بررسی کنید.
5. مثال‌های مرتبط را در پوشه [examples](examples/) مطالعه و روی سخت‌افزار هدف اعتبارسنجی کنید.

برای جزئیات ساخت، Featureها، نسخه Rust، مثال‌ها، جدول کامل Peripheralها و موارد در دست توسعه، بخش انگلیسی همین README و فایل [Cargo.toml](Cargo.toml) را نیز مطالعه کنید.

## منابع مرتبط

- [مخزن ch32-hal در سازمان MES](https://github.com/MEMAR-ELECTRONIC-SANAT/ch32-hal)
- [پروژه ch32-rs/ch32-data](https://github.com/ch32-rs/ch32-data)
- [چارچوب Embassy](https://github.com/embassy-rs/embassy)
- [مستندات و منابع CH32 در سازمان MES](https://github.com/MEMAR-ELECTRONIC-SANAT)

## جدول وضعیت پشتیبانی تراشه‌ها و واحدهای جانبی

جدول زیر وضعیت پشتیبانی واحدهای جانبی را در خانواده‌های مختلف میکروکنترلر نشان می‌دهد. این وضعیت باید در کنار نسخه فعلی کد و مستندات تراشه بررسی شود.

| واحد جانبی | V2/V3 | V1 | V003 | V00X | X0 | L1 | CH641 | CH643 | H4 |
|---|---|---|---|---|---|---|---|---|---|
| Embassy | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی |
| RCC | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی |
| GPIO | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی |
| PIOC | ندارد | ندارد | ندارد | ندارد | پشتیبانی | ندارد | ندارد | پشتیبانی | پشتیبانی |
| EXTI | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی |
| UART | پشتیبانی | پشتیبانی | پشتیبانی | آزمایش‌نشده | پشتیبانی | پشتیبانی | آزمایش‌نشده | آزمایش‌نشده | پشتیبانی |
| SPI | پشتیبانی | پشتیبانی | پشتیبانی | آزمایش‌نشده | پشتیبانی | پشتیبانی | ندارد | آزمایش‌نشده | پشتیبانی |
| I2C | پشتیبانی | پشتیبانی | پشتیبانی | آزمایش‌نشده | آزمایش‌نشده | آزمایش‌نشده | آزمایش‌نشده | آزمایش‌نشده | پشتیبانی |
| ADC | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی |
| Timer/PWM | پشتیبانی | پشتیبانی | پشتیبانی | آزمایش‌نشده | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی | پشتیبانی |
| IWDG / WWDG | پشتیبانی در V2/V3 | ندارد | ندارد | آزمایش‌نشده | ندارد | ندارد | ندارد | ندارد | آزمایش‌نشده |
| CRC | پشتیبانی در V2/V3 | ندارد | ندارد | ندارد | ندارد | ندارد | ندارد | ندارد | آزمایش‌نشده |
| USB Device | پشتیبانی محدود | ندارد | ندارد | ندارد | ندارد | ندارد | ندارد | ندارد | ندارد |
| USB OTG FS | پشتیبانی محدود | ندارد | ندارد | ندارد | پشتیبانی محدود | پشتیبانی محدود | ندارد | ندارد | پشتیبانی محدود |
| USB HS | پشتیبانی محدود | ندارد | ندارد | ندارد | ندارد | ندارد | ندارد | ندارد | آزمایش‌نشده |
| USB SS | ندارد | ندارد | ندارد | ندارد | ندارد | ندارد | ندارد | ندارد | پشتیبانی محدود |
| USB PD | ندارد | ندارد | ندارد | ندارد | پشتیبانی محدود | پشتیبانی محدود | آزمایش‌نشده | آزمایش‌نشده | غیرفعال |
| Ethernet | پشتیبانی محدود | ندارد | ندارد | ندارد | ندارد | ندارد | ندارد | ندارد | پشتیبانی محدود |
| CAN | پشتیبانی | ندارد | ندارد | ندارد | ندارد | پشتیبانی | ندارد | ندارد | پشتیبانی |

**راهنمای وضعیت‌ها:**
- ✅ پشتیبانی مورد انتظار یا پیاده‌سازی‌شده؛ لزوماً به معنی آزمون روی تمام مدل‌ها نیست.
- ❌ پیاده‌سازی نشده یا در این خانواده قابل استفاده نیست.
- ❓ هنوز روی سخت‌افزار آزمایش نشده است.
- **N/A** یعنی آن قابلیت در خانواده موردنظر وجود ندارد.
- علامت ستاره در جدول اصلی نشان‌دهنده درایور ناهمگام است. فعال‌سازی EXTI نیز به Feature جداگانه `exti` در Cargo نیاز دارد.
- قابلیت PIOC به Feature دستی `pioc` و یک تصویر Firmware نیاز دارد. این قابلیت روی X0 و H4 آزمایش شده و CH643 از پیاده‌سازی X0 پیروی می‌کند.
- در Ethernet، MAC+PHY ده مگابیتی CH32V208 و PHY داخلی 10/100 در H4 اعتبارسنجی شده‌اند. MAC مبتنی بر Synopsys DWC در V305/V307 در حال توسعه است و مسیر RGMII/Gigabit در H4 هنوز روی برد آزمایش نشده است.
- خانواده H4 مربوط به تراشه دو‌هسته‌ای CH32H417 با هسته‌های QingKe V3F و V5F است. USB FS از بلوک دستگاه OTG-v2 و درایور `otg_fs` استفاده می‌کند؛ USB HS از درایور V4x7 بهره می‌برد و عملکرد High Speed آن هنوز اعتبارسنجی نشده است. درایور USB SS برای دستگاه SuperSpeed در `embassy-usb` در دسترس است و حالت Host فعلاً فقط برقراری لینک را انجام می‌دهد. USB PD به دلیل تفاوت نقشه رجیسترها غیرفعال است. در H4، درایور I2C نسخه V3 قابل استفاده است. DMA در جدول خلاصه نشده و DMAMUX از شماره درخواست‌های تعریف‌شده در Metadata استفاده می‌کند. درایور زمان Embassy در H4 از SysTick استفاده می‌کند و به Feature از نوع `time-driver-timN` نیاز ندارد.
- عنوان V00X شامل CH32V002، CH32V004، CH32V005، CH32V006، CH32V007 و CH32M007 است.

## قابلیت‌های تکمیلی و محدودیت‌های شناخته‌شده

- رابط I2C علاوه بر حالت Blocking، از حالت Slave با وقفه و DMA نیز پشتیبانی می‌کند. نمونه آن در [i2c_slave.rs](examples/ch32l103/src/bin/i2c_slave.rs) قرار دارد و روی CH32L103 در ارتباط با CH32V203 به‌عنوان Master اعتبارسنجی شده است.
- برای USB OTG FS، USB HS و USB SS، فایل `mod.rs` مربوط به هر درایور را بررسی کنید تا قابلیت‌های پیاده‌سازی‌شده و موارد آزمایش‌نشده مشخص شوند.
- موارد توسعه باقی‌مانده شامل PLL2 برای CH32V3 و DMA2 برای CH32V3 است؛ DMA2 به مدیریت ویژه کانال‌های DMA با شماره بالا نیاز دارد.
- پشتیبانی از تراشه‌های جدید CH645 با USB HUB و SerDes خانواده V4C و CH564 با USBHS و Ethernet صد مگابیتی خانواده V4J به مشارکت توسعه‌دهندگان نیاز دارد.

<p align="center"><img src="Images/GitHub-Assets/MES-Project-Card.svg" width="100%" alt="معرفی پروژه‌های مبتنی بر ch32-hal"></p>

## پروژه‌های ساخته‌شده با ch32-hal

نمونه‌ای از پروژه‌های مبتنی بر این کتابخانه، [Hackoween 2024 badge](https://github.com/rappet/hackoween-badge) است. اگر پروژه‌ای با این HAL ساخته‌اید، می‌توانید آن را در بخش [Discussions](https://github.com/ch32-rs/ch32-hal/discussions) معرفی کنید؛ پروژه‌های متن‌باز می‌توانند برای اضافه‌شدن به این فهرست پیشنهاد شوند.

## نسخه موردنیاز Rust

این پروژه با نسخه نسبتاً جدیدی از کامپایلر **Rust nightly** توسعه داده می‌شود و انتظار می‌رود با نسخه‌های **beta** نیز کار کند. نسخه دقیق ابزارها و سازگاری آن‌ها را هنگام ساخت پروژه بررسی کنید.

## مشارکت در توسعه

از انواع مشارکت‌ها استقبال می‌شود؛ از جمله:
- معرفی پروژه‌های ساخته‌شده با این کتابخانه در بخش Discussions.
- بهبود README، مستندات و توضیحات مستنداتی داخل کد.
- نوشتن برنامه‌های نمونه برای Peripheralها.
- بازبینی و اصلاح تعریف واحدهای جانبی در مخزن [ch32-data](https://github.com/ch32-rs/ch32-data).
- توسعه درایورهای جدید با توجه به دشواری حفظ سازگاری بین خانواده‌های مختلف تراشه.

## مجوز

این پروژه بنا به انتخاب استفاده‌کننده، تحت مجوز **MIT** یا **Apache-2.0** منتشر شده است.

</div>

---

<div dir="ltr" align="left">

# ch32-hal

[![Demo Code Github Actions][badge-actions]][actions-build]

[badge-actions]: https://img.shields.io/github/actions/workflow/status/ch32-rs/ch32-hal/build.yml?style=for-the-badge&label=Demo%20Code%20Build
[actions-build]: https://github.com/ch32-rs/ch32-hal/actions/workflows/build.yml

Rust HAL(Hardware Abstraction Layer) crate for WCH's 32-bit RISC-V microcontrollers.

> **Note**
> This project is under development. While it's usable for experimentation and testing,
> it may not be fully stable for production environments.
> We welcome user feedback and encourage reporting any issues you encounter to help improve the hal crate.

This HAL crate provides an [Embassy](https://github.com/embassy-rs/embassy)-compatible driver implementation for WCH's 32-bit RISC-V microcontrollers.

This HAL crate uses the metapac approach to support multiple chips in a single crate.
The metapac is maintained in the [ch32-rs/ch32-data](https://github.com/ch32-rs/ch32-data) repository, published as a crate `ch32-metapac`.

Keypoints:

- Embassy support
- All-in-one metapac for peripheral register access, check [ch32-data](https://github.com/ch32-rs/ch32-data) for more
- All-in-one HAL crate, no need to create a new crate for each chip
- Async drivers, with async/await support, DMA support
- Write once, run on all supported chips(should be)

## Supported Devices and Peripherals

Currently, supported chips are listed in `Cargo.toml` as feature flags,
others should work if you are careful as most peripherals are similar enough.

For a full list of chip capabilities and peripherals, check the [ch32-data](https://github.com/ch32-rs/ch32-data) repository.

| Family     | V2/V3 | V1  | V003 | V00X | X0   | L1   | CH641 | CH643 | H4‡   |
| ---------- | ----- | --- | ---- | ---- | ---- | ---- | ----- | ----- | ---- |
| Embassy    | ✅    | ✅  | ✅   | ✅   | ✅   | ✅   | ✅    | ✅    | ✅    |
| RCC        | ✅    | ✅  | ✅   | ✅   | ✅   | ✅   | ✅    | ✅    | ✅    |
| GPIO       | ✅    | ✅  | ✅   | ✅   | ✅   | ✅   | ✅    | ✅    | ✅    |
| PIOC§      | N/A   | N/A | N/A  | N/A  | ✅   | N/A  | N/A   | ✅    | ✅   |
| EXTI\*     | ✅    | ✅  | ✅   | ✅   | ✅   | ✅   | ✅    | ✅    | ✅    |
| UART\*     | ✅    | ✅  | ✅   | ❓   | ✅   | ✅   | ❓    | ❓    | ✅    |
| SPI\*      | ✅    | ✅  | ✅   | ❓   | ✅   | ✅   | N/A   | ❓    | ✅\*  |
| I2C        | ✅    | ✅  | ✅   | ❓   | ❓   | ❓   | ❓    | ❓    | ✅    |
| ADC        | ✅    | ✅  | ✅   | ✅   | ✅   | ✅   | ✅    | ✅    | ✅    |
| Timer(PWM) | ✅    | ✅  | ✅   | ❓   | ✅   | ✅   | ✅    | ✅    | ✅    |
| IWDG       | ✅    | N/A | N/A  | ❓   | N/A  | N/A  | N/A   | N/A   | ❓    |
| WWDG       | ✅    | N/A | N/A  | ❓   | N/A  | N/A  | N/A   | N/A   | ❓    |
| CRC        | ✅    | N/A | N/A  | N/A  | N/A  | N/A  | N/A   | N/A   | ❓    |
| USBD       | ✅\*  | N/A | N/A  | N/A  | N/A  | N/A  | N/A   | N/A   | N/A  |
| USB/OTG FS | ✅\*  | N/A | N/A  | N/A  | ✅\* | ✅\* | N/A   | N/A   | ✅\*  |
| USB HS     | ✅\*  | N/A | N/A  | N/A  | N/A  | N/A  | N/A   | N/A   | ❓\*  |
| USB SS     | N/A   | N/A | N/A  | N/A  | N/A  | N/A  | N/A   | N/A   | ✅\*  |
| USB PD     | N/A   | N/A | N/A  | N/A  | ✅\* | ✅\* | ❓    | ❓    | ❌    |
| ETH\*      | ✅†   | N/A | N/A  | N/A  | N/A  | N/A  | N/A   | N/A   | ✅†   |
| CAN\*      | ✅    | N/A | N/A  | N/A  | N/A  | ✅   | N/A   | N/A   | ✅    |

- ✅ : Expected to work
- ❌ : Not implemented
- ❓ : Not tested
- `*` marks the async driver (EXTI is also opt-in via the `exti` Cargo feature)
- `§` PIOC is behind the manual `pioc` Cargo feature and needs a firmware image;
  X0 and H4 are board-tested, CH643 follows X0.
- `†` ETH: the CH32V208 10M MAC+PHY and the H4 internal 10/100 PHY are
  validated; the V305/V307 Synopsys DWC MAC is in progress (PR #157), and the
  H4 RGMII/Gigabit path is not board-tested.
- `‡` H4 is the dual-core CH32H417 (QingKe V3F + V5F). USB FS is the OTG-v2
  device block (`usbfs_h4`, reusing the `otg_fs` driver); USB HS reuses the
  V4x7 driver (`usbhs_h4`) and has not been validated at High Speed. USB SS
  (`usbss`) is a SuperSpeed device driver for `embassy-usb`; host mode only
  brings the link up. USB PD is compiled out because the H4 register layout
  differs. I2C is flagged `version: v3` in the H4 metadata, so the V3 driver
  applies as-is. DMA is not in the table; the H4 DMAMUX is driven from
  request numbers in metadata. The H4 Embassy time driver is
  `time_driver_systick_h4`, so no `time-driver-timN` feature is needed.
- N/A: Not available

**V00X** covers CH32V002 / CH32V004 / CH32V005 / CH32V006 / CH32V007 and CH32M007.

I2C also has slave support (blocking, interrupt and DMA); see
[`examples/ch32l103/src/bin/i2c_slave.rs`](examples/ch32l103/src/bin/i2c_slave.rs),
validated on the CH32L103 against a CH32V203 master.

Dual-core examples for the CH32H417 (separate V3F and V5F images, a shared
mailbox, `xtask` for flash/report) live in [`examples/ch32h417`](examples/ch32h417).

### Notes

- For USB OTGFS, HS and SS, look at the respective `mod.rs` to understand what is / is not tested.

### Important: ROM/RAM Split Configuration (CH32V2/V3)

> **Warning**
> All CH32V2 and CH32V3 series chips support configurable ROM/RAM split. The configuration is stored in
> `FLASH_OBR.RAM_CODE_MOD` register (read-only at runtime, can only be modified via external tools).
>
> If your program crashes immediately after flashing (e.g., Store Access Fault at stack addresses),
> your chip's ROM/RAM configuration likely doesn't match the linker script.

**Symptoms:**

- Program crashes with `mcause=0x7` (Store/AMO access fault)
- Stack pointer (`sp`) points to invalid memory region
- No output or immediate crash after reset

**Solution:**
Use WCH's official tool (WCHISPTool) to configure the ROM/RAM split to match the default values defined in [ch32-data](https://github.com/ch32-rs/ch32-data).

The linker script expects the **default RAM size** for each chip. Check your chip's YAML definition in ch32-data for the expected memory layout.

### TODOs

This section lists some key items that are not implemented yet. And should be noted when using this crate.

- PLL2 for CH32V3
- DMA2 for CH32V3 (requires special handling of high DMA channels)

### Coming New Chips - Help Wanted

- CH645, USB HUB, SerDes (V4C)
- CH564, USBHS, 100M Ethernet (V4J)

## Built with ch32-hal ✨

This is a list for awesome projects that are built using ch32-hal

- [Hackoween 2024 badge](https://github.com/rappet/hackoween-badge)

## Minimum supported Rust version(MSRV)

This project is developed with a recent **nightly** version of Rust compiler. And is expected to work with beta versions of Rust.

Feel free to change this if you did some testing with some version of Rust.

## Contributing

All kinds of contributions are welcome.

- Share your project at [Discussions](https://github.com/ch32-rs/ch32-hal/discussions)
  - if your project is an open-source project, consider adding it to the list above
- README and Documentation, including doc comments in code
- Writing demo code for peripherals
- Revising the peripheral definitions at [ch32-data](https://github.com/ch32-rs/ch32-data)
- Adding new peripheral drivers - This is difficult to make it compatible with all chips, but you can try.
- ...

## License

This project is licensed under the MIT or Apache-2.0 license, at your option.

---

## Upstream Projects and Credits

This repository builds on the work of the upstream **ch32-rs** project and its supporting ecosystem:

- [ch32-rs/ch32-hal](https://github.com/ch32-rs/ch32-hal) — upstream HAL implementation and development history.
- [ch32-rs/ch32-data](https://github.com/ch32-rs/ch32-data) — chip metadata, peripheral definitions, and register information used by `ch32-metapac`.
- [Embassy](https://github.com/embassy-rs/embassy) — the asynchronous embedded framework integrated by this HAL.

Please refer to the upstream repositories for original implementation details, development discussions, and contribution history.

## Images and Reference Diagrams

<p align="center"><img src="Images/GitHub-Assets/MES-Section-Header.svg" width="100%" alt="MES Section Header"></p>

The visual identity assets for MES are stored in [Images/GitHub-Assets](Images/GitHub-Assets/). These self-contained SVG graphics can be embedded in GitHub README files using HTML `img` elements. Animation support may vary depending on the browser and GitHub rendering context.

| File | Purpose |
|---|---|
| [MES-Hero-Banner.svg](Images/GitHub-Assets/MES-Hero-Banner.svg) | Main README or profile banner |
| [MES-Divider.svg](Images/GitHub-Assets/MES-Divider.svg) | Horizontal divider between sections |
| [MES-Section-Header.svg](Images/GitHub-Assets/MES-Section-Header.svg) | Visual heading for important sections |
| [MES-Project-Card.svg](Images/GitHub-Assets/MES-Project-Card.svg) | Project introduction and showcase |
| [MES-Status-Strip.svg](Images/GitHub-Assets/MES-Status-Strip.svg) | Project status and resource strip |
| [MES-Thank-You-Footer.svg](Images/GitHub-Assets/MES-Thank-You-Footer.svg) | Closing artwork at the end of a README |
| [MES_Animated_Logo_640x480.svg](Images/GitHub-Assets/MES_Animated_Logo_640x480.svg) | Animated MES logo |

<p align="center"><img src="Images/GitHub-Assets/MES-Thank-You-Footer.svg" width="100%" alt="MES Thank You Footer"></p>

## Prepared and Maintained By

**MEMAR ELECTRONIC SANAT (MES)**

*Your partner in designing and building reliable circuits.*

- [Telegram](https://t.me/Memar_Electronic)
- [Instagram](https://www.instagram.com/Memar.Electronic)
- [Explore more tutorials and projects](https://github.com/MEMAR-ELECTRONIC-SANAT)

<p align="center">
  <img src="Images/GitHub-Assets/MES_Animated_Logo_640x480.svg" width="180" alt="MES Animated Logo">
</p>

</div>

<div dir="rtl" align="right">

## تصاویر و دیاگرام‌های راهنما

<p align="center"><img src="Images/GitHub-Assets/MES-Section-Header.svg" width="100%" alt="عنوان بخش تصاویر و دیاگرام‌های MES"></p>

فایل‌های گرافیکی و SVG متحرک هویت بصری MES در پوشه [Images/GitHub-Assets](Images/GitHub-Assets/) نگهداری می‌شوند. این فایل‌ها برای استفاده در README مخازن و معرفی پروژه‌ها طراحی شده‌اند و می‌توان آن‌ها را با تگ HTML یعنی `img` در GitHub نمایش داد. نمایش انیمیشن ممکن است با توجه به مرورگر و محیط نمایش GitHub متفاوت باشد.

| فایل | کاربرد |
|---|---|
| [MES-Hero-Banner.svg](Images/GitHub-Assets/MES-Hero-Banner.svg) | سربرگ اصلی README یا پروفایل |
| [MES-Divider.svg](Images/GitHub-Assets/MES-Divider.svg) | جداکننده افقی میان بخش‌ها |
| [MES-Section-Header.svg](Images/GitHub-Assets/MES-Section-Header.svg) | عنوان بصری بخش‌های مهم |
| [MES-Project-Card.svg](Images/GitHub-Assets/MES-Project-Card.svg) | معرفی و نمایش پروژه |
| [MES-Status-Strip.svg](Images/GitHub-Assets/MES-Status-Strip.svg) | نوار وضعیت پروژه و منابع |
| [MES-Thank-You-Footer.svg](Images/GitHub-Assets/MES-Thank-You-Footer.svg) | تصویر پایانی README |
| [MES_Animated_Logo_640x480.svg](Images/GitHub-Assets/MES_Animated_Logo_640x480.svg) | لوگوی متحرک MES |

<p align="center"><img src="Images/GitHub-Assets/MES-Thank-You-Footer.svg" width="100%" alt="پایان‌بندی MES"></p>

## تهیه و تدوین

**معمار الکترونیک صنعت / MEMAR ELECTRONIC SANAT (MES)**

همراه شما در مسیر طراحی و ساخت مدارهای مطمئن

- [Telegram](https://t.me/Memar_Electronic)
- [Instagram](https://www.instagram.com/Memar.Electronic)
- [مشاهده سایر آموزش‌ها](https://github.com/MEMAR-ELECTRONIC-SANAT)

<p align="center">
  <img src="Images/GitHub-Assets/MES_Animated_Logo_640x480.svg" width="180" alt="MES Animated Logo">
</p>

</div>
