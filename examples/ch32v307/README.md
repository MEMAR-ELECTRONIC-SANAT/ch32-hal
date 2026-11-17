<div dir="rtl" align="right">

## معرفی و راهنمای نمونه‌برنامه‌های CH32V307

این پوشه نمونه‌برنامه‌های پیشرفته‌تر برای میکروکنترلر **CH32V307** را در بر می‌گیرد. علاوه بر معرفی برد `YD-CH32V307VCT6`، اتصالات حافظه خارجی، کارت حافظه، Ethernet و دو نمونه آزمایشی USB High-Speed در این بخش شرح داده شده‌اند.

## برد YD-CH32V307VCT6

### کلاک و نشانگرهای وضعیت

- کریستال خارجی پرسرعت (HSE): `8MHz`
- کریستال خارجی کم‌سرعت (LSE): `32.768KHz`
- LED قرمز: پایه `PA15`، فعال با سطح منطقی بالا (Active High)
- LED آبی: پایه `PB4`، فعال با سطح منطقی بالا (Active High)
- کلید کاربر: پایه `PB3`، فعال با سطح منطقی پایین (Active Low)

### حافظه EEPROM از نوع FT24C32 – رابط I2C2

تراشه `FT24C32` از طریق رابط `I2C2` و با مقاومت‌های Pull-up متصل شده است:

- `SCL`: پایه `PB10`
- `SDA`: پایه `PB11`

### حافظه Flash خارجی W25Q16 – رابط SPI1

تراشه `W25Q16` از رابط `SPI1` استفاده می‌کند:

- `CS`: پایه `PA2`
- `DO (IO1)`: پایه `PA6`
- `CLK`: پایه `PA5`
- `DI (IO0)`: پایه `PA7`

### کارت Micro SD / TF – رابط SDIO

اتصال کارت حافظه از طریق رابط `SDIO` انجام می‌شود:

- `DAT0`: پایه `PC8`؛ برای فعال‌سازی باید جامپر پشت برد لحیم شود.
- `DAT1`: پایه `PC9`؛ برای فعال‌سازی باید جامپر پشت برد لحیم شود.
- `DAT2`: پایه `PC10`
- `DAT3`: پایه `PC11`
- `CLK`: پایه `PC12`
- `CMD`: پایه `PD2`
- `TFSW`: پایه `PD7`؛ برای فعال‌سازی باید جامپر پشت برد لحیم شود.

پیش از استفاده از کارت حافظه، وضعیت جامپرهای مربوط به `DAT0`، `DAT1` و `TFSW` را بررسی کنید.

### رابط Ethernet

برای فعال‌سازی اتصال Ethernet نیز باید جامپر مربوطه در پشت برد لحیم شود. تخصیص پایه‌های رابط شبکه به شرح زیر است:

- LED اتصال (LINK): پایه `PC0`
- LED فعالیت (ACT): پایه `PC1`
- سیگنال دریافت منفی (`RXN`): پایه `PC7`
- سیگنال دریافت مثبت (`RXP`): پایه `PC6`
- سیگنال ارسال منفی (`TXN`): پایه `PC9`
- سیگنال ارسال مثبت (`TXP`): پایه `PC8`

## هدف کامپایل و معماری پردازنده

فایل هدف [`riscv32imfc-unknown-none-elf.json`](riscv32imfc-unknown-none-elf.json) برای این پروژه استفاده می‌شود. این مشخصات، هدف کامپایل bare-metal را تعیین و افزونه `xwchc` را فعال می‌کند. این افزونه دستورهای فشرده اختصاصی WCH برای بارگذاری و ذخیره‌سازی بایت و نیم‌کلمه را در اختیار کامپایلر قرار می‌دهد. هسته `QingKe V4F` از این قابلیت پشتیبانی می‌کند؛ مرجع ذکرشده برای جزئیات، جدول ۱-۱ راهنمای هسته `QingKe V3` است.

## نمونه USBHS Isochronous Loopback

فایل `src/bin/usb_hs_iso.rs` یک دستگاه USB با کلاس اختصاصی سازنده (Vendor Class) و یک Interface پیاده‌سازی می‌کند. این Interface دو Alternate Setting دارد:

- **Alternate Setting 0:** حالت غیرفعال (Idle) و بدون Endpoint.
- **Alternate Setting 1:** دارای یک Endpoint از نوع Isochronous برای دریافت (OUT) و یک Endpoint از همین نوع برای ارسال (IN). اندازه هر Packet برابر ۶۴ بایت است و در سرعت High-Speed، در هر Microframe یک Packet منتقل می‌شود.

Packetهای ورودی OUT توسط یک Task خوانده و تخلیه می‌شوند. تابع `read()` داده‌ها را دریافت می‌کند و وقفه از یک Ring Buffer با ظرفیت ۱۶ Slot استفاده می‌کند.

Packetهای خروجی IN در Callback مربوط به SOF و با تابع `iso_in_fill()` ساخته می‌شوند. محتوای Packet شامل این اطلاعات است:

- شماره ترتیبی Packet ارسالی IN
- آخرین شماره ترتیبی Packet دریافتی OUT
- شماره Microframe
- تعداد Packetهای OUT دریافت‌شده
- تعداد سرریزهای Ring Buffer مربوط به OUT

برای آزمایش، میزبان باید از انتقال Isochronous پشتیبانی کند. برای مثال، Linux به‌همراه `libusb` قابل استفاده است؛ اما macOS از انتقال Isochronous از طریق `libusb` پشتیبانی نمی‌کند. میزبان می‌تواند Alternate Setting شماره ۱ را فعال کند و بررسی کند که شماره‌های ترتیبی IN تکراری نباشند و شماره OUT بازتاب‌داده‌شده فقط در جهت افزایشی حرکت کند.

## نمونه USBHS Interrupt Endpoint Echo

فایل `src/bin/usb_hs_int.rs` یک دستگاه USB با کلاس اختصاصی سازنده ایجاد می‌کند که دارای دو Endpoint از نوع Interrupt است:

- Endpoint دریافت OUT با آدرس `0x01`
- Endpoint ارسال IN با آدرس `0x81`
- اندازه Packet برابر ۶۴ بایت

Task برنامه یک Packet را از Endpoint OUT می‌خواند و همان داده را از طریق Endpoint IN به میزبان بازمی‌گرداند؛ بنابراین این نمونه عملکرد Echo را برای انتقال Interrupt بررسی می‌کند.

با استفاده از `libusb` در Linux یا macOS می‌توان Packetهای ۶۴ بایتی، کوتاه‌تر و حتی صفر بایتی ارسال کرد و داده دریافتی را با داده ارسالی مقایسه کرد. این آزمون مسیرهای آماده‌سازی و آماده‌به‌کارشدن Interrupt، کنترل جریان با NAK و مدیریت Toggle سخت‌افزاری را بررسی می‌کند.

## نکات مهم راه‌اندازی برد

- سطح فعال LEDها و کلید را در برنامه رعایت کنید؛ LEDها Active High و کلید Active Low هستند.
- رابط‌های I2C2، SPI1 و SDIO از پایه‌های مشخص‌شده در بالا استفاده می‌کنند؛ در زمان استفاده هم‌زمان از Peripheralها، تداخل تخصیص پایه‌ها را بررسی کنید.
- جامپرهای پشت برد برای برخی خطوط کارت SD و Ethernet ضروری هستند.
- برای تست USBHS، تفاوت پشتیبانی سیستم‌عامل میزبان از انتقال‌های Isochronous و Interrupt را در نظر بگیرید؛ محدودیت macOS در این README فقط به Isochronous از طریق `libusb` اشاره دارد.

</div>

---

## Folder Overview

Example applications and test projects for the CH32V307 microcontroller using ch32-hal.

---

# ch32v307-examples

## Boards

### YD-CH32V307VCT6

8MHz XTAL, 32.768KHz XTAL

- PA15, Red LED, active high
- PB4, Blue LED, active high
- PB3, User button, active low

FT24C32(I2C2 with pull up)

- SCL: PB10
- SDA: PB11

W25Q16(SPI1)

- CS: PA2
- DO(IO1): PA6
- CLK: PA5
- DI(IO0): PA7

Micro SD Card / TFCard (SDIO)

- DAT0: PC8 (requires soldering the jumper on the back of the board)
- DAT1: PC9 (requires soldering the jumper on the back of the board)
- DAT2: PC10
- DAT3: PC11
- CLK: PC12
- CMD: PD2

- TFSW: PD7 (requires soldering the jumper on the back of the board)

Ethernet  (requires soldering the jumper on the back of the board)

- PC0, LINK LED
- PC1, ACT LED
- PC7, RXN
- PC6, RXP
- PC9, TXN
- PC8, TXP

## Target

[`riscv32imfc-unknown-none-elf.json`](riscv32imfc-unknown-none-elf.json) enables `xwchc`, WCH's compressed byte and halfword loads and stores. QingKe V4F implements the extension (QingKe V3 manual, table 1-1).

## USBHS isochronous loopback

`src/bin/usb_hs_iso.rs` is a vendor-class device with one interface:

- alt setting 0: idle, no endpoints
- alt setting 1: isochronous OUT + IN, 64-byte packets, one packet per microframe at high speed

OUT packets are drained by a task (`read()`, 16-slot ring in the interrupt); IN packets are
built in the SOF callback (`iso_in_fill()`) and carry the IN sequence, the last OUT sequence
seen, the microframe number, the OUT packet count and the OUT ring's overrun count. A host
with isochronous transfer support (for example Linux with libusb; macOS does not support
isochronous transfers through libusb) can set alt setting 1 and check that IN sequences never
repeat and that the echoed OUT sequence only moves forward.

## USBHS interrupt endpoint echo

`src/bin/usb_hs_int.rs` is a vendor-class device with an interrupt OUT (0x01) and an
interrupt IN (0x81) endpoint, 64-byte packets. The task reads one OUT packet and writes it
back on the IN endpoint. A host using libusb (macOS or Linux both support interrupt
transfers) can send 64-byte, short and zero-length packets and compare the echo; this
exercises the interrupt arm/ready path, NAK flow control and the hardware toggle handling.

<div dir="rtl" align="right">

## معرفی و راهنمای نمونه‌برنامه‌های CH32V307

این پوشه نمونه‌برنامه‌های پیشرفته‌تر برای میکروکنترلر **CH32V307** را در بر می‌گیرد. علاوه بر معرفی برد `YD-CH32V307VCT6`، اتصالات حافظه خارجی، کارت حافظه، Ethernet و دو نمونه آزمایشی USB High-Speed در این بخش شرح داده شده‌اند.

## برد YD-CH32V307VCT6

### کلاک و نشانگرهای وضعیت

- کریستال خارجی پرسرعت (HSE): `8MHz`
- کریستال خارجی کم‌سرعت (LSE): `32.768KHz`
- LED قرمز: پایه `PA15`، فعال با سطح منطقی بالا (Active High)
- LED آبی: پایه `PB4`، فعال با سطح منطقی بالا (Active High)
- کلید کاربر: پایه `PB3`، فعال با سطح منطقی پایین (Active Low)

### حافظه EEPROM از نوع FT24C32 – رابط I2C2

تراشه `FT24C32` از طریق رابط `I2C2` و با مقاومت‌های Pull-up متصل شده است:

- `SCL`: پایه `PB10`
- `SDA`: پایه `PB11`

### حافظه Flash خارجی W25Q16 – رابط SPI1

تراشه `W25Q16` از رابط `SPI1` استفاده می‌کند:

- `CS`: پایه `PA2`
- `DO (IO1)`: پایه `PA6`
- `CLK`: پایه `PA5`
- `DI (IO0)`: پایه `PA7`

### کارت Micro SD / TF – رابط SDIO

اتصال کارت حافظه از طریق رابط `SDIO` انجام می‌شود:

- `DAT0`: پایه `PC8`؛ برای فعال‌سازی باید جامپر پشت برد لحیم شود.
- `DAT1`: پایه `PC9`؛ برای فعال‌سازی باید جامپر پشت برد لحیم شود.
- `DAT2`: پایه `PC10`
- `DAT3`: پایه `PC11`
- `CLK`: پایه `PC12`
- `CMD`: پایه `PD2`
- `TFSW`: پایه `PD7`؛ برای فعال‌سازی باید جامپر پشت برد لحیم شود.

پیش از استفاده از کارت حافظه، وضعیت جامپرهای مربوط به `DAT0`، `DAT1` و `TFSW` را بررسی کنید.

### رابط Ethernet

برای فعال‌سازی اتصال Ethernet نیز باید جامپر مربوطه در پشت برد لحیم شود. تخصیص پایه‌های رابط شبکه به شرح زیر است:

- LED اتصال (LINK): پایه `PC0`
- LED فعالیت (ACT): پایه `PC1`
- سیگنال دریافت منفی (`RXN`): پایه `PC7`
- سیگنال دریافت مثبت (`RXP`): پایه `PC6`
- سیگنال ارسال منفی (`TXN`): پایه `PC9`
- سیگنال ارسال مثبت (`TXP`): پایه `PC8`

## هدف کامپایل و معماری پردازنده

فایل هدف [`riscv32imfc-unknown-none-elf.json`](riscv32imfc-unknown-none-elf.json) برای این پروژه استفاده می‌شود. این مشخصات، هدف کامپایل bare-metal را تعیین و افزونه `xwchc` را فعال می‌کند. این افزونه دستورهای فشرده اختصاصی WCH برای بارگذاری و ذخیره‌سازی بایت و نیم‌کلمه را در اختیار کامپایلر قرار می‌دهد. هسته `QingKe V4F` از این قابلیت پشتیبانی می‌کند؛ مرجع ذکرشده برای جزئیات، جدول ۱-۱ راهنمای هسته `QingKe V3` است.

## نمونه USBHS Isochronous Loopback

فایل `src/bin/usb_hs_iso.rs` یک دستگاه USB با کلاس اختصاصی سازنده (Vendor Class) و یک Interface پیاده‌سازی می‌کند. این Interface دو Alternate Setting دارد:

- **Alternate Setting 0:** حالت غیرفعال (Idle) و بدون Endpoint.
- **Alternate Setting 1:** دارای یک Endpoint از نوع Isochronous برای دریافت (OUT) و یک Endpoint از همین نوع برای ارسال (IN). اندازه هر Packet برابر ۶۴ بایت است و در سرعت High-Speed، در هر Microframe یک Packet منتقل می‌شود.

Packetهای ورودی OUT توسط یک Task خوانده و تخلیه می‌شوند. تابع `read()` داده‌ها را دریافت می‌کند و وقفه از یک Ring Buffer با ظرفیت ۱۶ Slot استفاده می‌کند.

Packetهای خروجی IN در Callback مربوط به SOF و با تابع `iso_in_fill()` ساخته می‌شوند. محتوای Packet شامل این اطلاعات است:

- شماره ترتیبی Packet ارسالی IN
- آخرین شماره ترتیبی Packet دریافتی OUT
- شماره Microframe
- تعداد Packetهای OUT دریافت‌شده
- تعداد سرریزهای Ring Buffer مربوط به OUT

برای آزمایش، میزبان باید از انتقال Isochronous پشتیبانی کند. برای مثال، Linux به‌همراه `libusb` قابل استفاده است؛ اما macOS از انتقال Isochronous از طریق `libusb` پشتیبانی نمی‌کند. میزبان می‌تواند Alternate Setting شماره ۱ را فعال کند و بررسی کند که شماره‌های ترتیبی IN تکراری نباشند و شماره OUT بازتاب‌داده‌شده فقط در جهت افزایشی حرکت کند.

## نمونه USBHS Interrupt Endpoint Echo

فایل `src/bin/usb_hs_int.rs` یک دستگاه USB با کلاس اختصاصی سازنده ایجاد می‌کند که دارای دو Endpoint از نوع Interrupt است:

- Endpoint دریافت OUT با آدرس `0x01`
- Endpoint ارسال IN با آدرس `0x81`
- اندازه Packet برابر ۶۴ بایت

Task برنامه یک Packet را از Endpoint OUT می‌خواند و همان داده را از طریق Endpoint IN به میزبان بازمی‌گرداند؛ بنابراین این نمونه عملکرد Echo را برای انتقال Interrupt بررسی می‌کند.

با استفاده از `libusb` در Linux یا macOS می‌توان Packetهای ۶۴ بایتی، کوتاه‌تر و حتی صفر بایتی ارسال کرد و داده دریافتی را با داده ارسالی مقایسه کرد. این آزمون مسیرهای آماده‌سازی و آماده‌به‌کارشدن Interrupt، کنترل جریان با NAK و مدیریت Toggle سخت‌افزاری را بررسی می‌کند.

## نکات مهم راه‌اندازی برد

- سطح فعال LEDها و کلید را در برنامه رعایت کنید؛ LEDها Active High و کلید Active Low هستند.
- رابط‌های I2C2، SPI1 و SDIO از پایه‌های مشخص‌شده در بالا استفاده می‌کنند؛ در زمان استفاده هم‌زمان از Peripheralها، تداخل تخصیص پایه‌ها را بررسی کنید.
- جامپرهای پشت برد برای برخی خطوط کارت SD و Ethernet ضروری هستند.
- برای تست USBHS، تفاوت پشتیبانی سیستم‌عامل میزبان از انتقال‌های Isochronous و Interrupt را در نظر بگیرید؛ محدودیت macOS در این README فقط به Isochronous از طریق `libusb` اشاره دارد.

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
