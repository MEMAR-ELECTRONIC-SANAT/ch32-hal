<div dir="rtl" align="right">

## معرفی پوشه

نمونه‌های CH32H417 دو‌هسته‌ای شامل Firmware هسته‌های V3F و V5F، ارتباط بین‌هسته‌ای (IPC)، ابزار xtask و آزمون Peripheralها.

</div>

<div dir="rtl" align="right">

## راهنمای فارسی

# نمونه‌های CH32H417

میکروکنترلر **CH32H417** دارای دو هسته پردازشی RISC-V است. به همین دلیل، نمونه‌ها بر اساس تراشه سازمان‌دهی شده‌اند، نه به‌صورت دو پروژه مستقل برای هر هسته:

| مسیر | کاربرد |
|---|---|
| `v3f/` | هسته راه‌انداز و کنترل، با شناسه hart 0 و معماری `riscv32imafc`؛ تصویر بارگذاری در Flash از نشانی `0x00000000` آغاز می‌شود و ۶۴ کیلوبایت فضا دارد. |
| `v5f/` | هسته دوم، با شناسه hart 1 و معماری `riscv32imafbc`؛ تصویر آن از نشانی `0x00010000` در Flash قرار می‌گیرد. |
| `ipc/` | تعریف mailbox مشترک برای تبادل داده و پیام بین دو هسته. |
| `xtask/` | ابزار مدیریت ساخت، پروگرام، اجرا، ادغام تصاویر و گزارش نتایج. |
| `out/` | محل ذخیره فایل‌های ادغام‌شده؛ این پوشه به‌صورت خودکار تولید می‌شود. |

پوشه‌های `v3f/` و `v5f/` دو crate مستقل هستند؛ زیرا مجموعه دستورالعمل، ABI و فایل `memory.x` آن‌ها متفاوت است. با این حال، هر دو از یک تراشه Flash و یک mailbox در RAM مشترک استفاده می‌کنند و ابزار `xtask` مراحل مربوط به آن‌ها را مدیریت می‌کند.

## دو نوع ساختار برای نمونه‌ها

پس از Reset، فقط هسته **V3F** شروع به اجرا می‌کند. هسته V5F تا زمانی که هسته V3F رجیستر بیدارباش آن، یعنی `PFIC_WAKEIP1`، را ننویسد غیرفعال می‌ماند. بنابراین نمونه‌ها دو ساختار دارند:

- **تک‌هسته‌ای (v3f-only):** فقط فایل `v3f/src/bin/NAME.rs` وجود دارد.
- **دو‌هسته‌ای (dual-core):** برای هر دو هسته فایل متناظر `src/bin/NAME.rs` وجود دارد.

نمونه‌ای که فقط فایل V5F داشته باشد معتبر نیست؛ زیرا هسته دوم به‌تنهایی و مستقیماً پس از Reset اجرا نمی‌شود.

| نوع | فایل‌های موردنیاز | نمونه‌ها |
|---|---|---|
| تک‌هسته‌ای | فقط `v3f/src/bin/NAME.rs` | `adc_check`، `blinky`، `bme280_blocking`، `bus_check`، `can_async_check`، `can_check`، `dac_check`، `delay_systick`، `dma2_check`، `dma_m2m`، `embassy_systick`، `esig_check`، `exti_check`، `flash_check`، `i2c_scan`، `launcher`، `pioc_text`، `pioc_ws2812`، `rcc_delay_check`، `rng_check`، `rtc_check`، `rtc_hse_check`، `sdi_print`، `sdio_check`، `sdio_exfat`، `sdio_fat`، `spi_check`، `timer_check` |
| دو‌هسته‌ای | فایل متناظر برای هر دو هسته | `atomics`، `bme280`، `cpuid`، `dualcore`، `hello`، `pingpong`، `sdi_cpuid` |

## نمونه‌های PIOC

نمونه‌های `pioc_ws2812` و `pioc_text` یک پنل WS2812 با آرایش ۸×۸ را از طریق Firmware مربوط به RGB1W شرکت WCH راه‌اندازی می‌کنند. این Firmware داخل واحد **PIOC** اجرا می‌شود، نه روی هسته RISC-V.

برای ساخت این دو نمونه، فعال بودن قابلیت `embassy` ضروری است. همچنین قابلیت دستی `pioc` در HAL موردنیاز است که در تنظیمات crate فعال شده است.

- پنل، ورودی IO0 را روی چهار پایه پشتیبانی می‌کند. در این پروژه `pioc-io0-pe3` به‌صورت پیش‌فرض انتخاب شده است؛ زیرا پایه PE3 با سطح ۳٫۳ ولت کار می‌کند و روی بردهای مورد استفاده در دسترس است. گزینه `pioc-io0-pf12` مناسب نیست؛ زیرا پایه‌های PF11 تا PF14 در دامنه تغذیه VIO18 قرار دارند و فقط سطح ۱٫۸ ولت ارائه می‌کنند.
- فایل `v3f/src/panel.rs` آرایش زنجیره LEDها با الگوی `RowZigzag` و حالت آینه‌ای، به‌همراه چرخه رنگ مشترک دو نمونه را تعریف می‌کند. انتخاب آرایش نادرست باعث جابه‌جایی یا قرینه شدن تصویر می‌شود؛ این ایراد با یک الگوی نامتقارن مانند متن بهتر دیده می‌شود.
- فایل `firmware/rgb1w-100m.pio` ترجمه نسخه اصلی `RGB1W.ASM` از بسته نرم‌افزاری WCH به دستور زبان استاندارد است. خروجی مونتاژ آن باید دقیقاً با فایل `RGB1W.BIN` شرکت سازنده یکسان باشد. حلقه‌های تأخیر با دستورهای `nop` تنظیم شده‌اند؛ بنابراین Firmware به کلاک ۱۰۰ مگاهرتز PIOC وابسته است.

در نمونه دو‌هسته‌ای، ابتدا بخش V3F راه‌اندازی اولیه تراشه، کلاک‌ها و GPIO را انجام می‌دهد، سپس کنترل را به hart 1 واگذار می‌کند و نتایج را گزارش می‌دهد. بخش V5F به crate مربوط به `ch32-hal` وابسته است، اما نباید تابع `ch32_hal::init` را اجرا کند؛ زیرا این تابع PLL را دوباره پیکربندی می‌کند و ممکن است با انتظار برای `CFGR0.SWS == PLL`، کلاک سیستمی را در زمانی تغییر دهد که هسته دیگر در حال اجراست. در عوض باید از `ch32_hal::init_secondary` استفاده شود. این تابع فقط درخت کلاک تنظیم‌شده توسط هسته راه‌انداز را می‌خواند و آن را در `rcc::clocks()` در دسترس قرار می‌دهد.

ماژول `ch32h417_v5f::pac`، متاپک HAL را دوباره صادر می‌کند. هر تصویر اجرایی بافر مستقل و غیرمسدودکننده defmt RTT خود را دارد. با وجود این، دو هسته نباید هم‌زمان از یک Peripheral استفاده کنند؛ زیرا هر تصویر singleton مستقل `Peripherals` دارد و از مالکیت Peripheral در هسته دیگر اطلاع پیدا نمی‌کند.

## فرمان‌های ساخت، پروگرام و اجرا

فرمان‌ها را از مسیر `examples/ch32h417` اجرا کنید:

```sh
cd examples/ch32h417

cargo xtask build  --example cpuid                 # فقط هسته راه‌انداز؛ حالت پیش‌فرض
cargo xtask build  --example cpuid --dual-core     # ساخت هر دو بخش نمونه
cargo xtask build  --example cpuid --jump-v5f      # ساخت jumper به‌همراه بخش V5F نمونه
cargo xtask build                                  # ساخت همه نمونه‌ها

cargo xtask flash  --example dualcore --dual-core  # ساخت و پروگرام هر دو تصویر، بدون کنسول
cargo xtask flash  --example eth_bench --flasher probe-rs  # استفاده از probe-rs برای بررسی نوشتن
cargo xtask run    --example dualcore --dual-core  # پروگرام و اجرا همراه کنسول RTT هسته V3F
cargo xtask flash  --example cpuid --jump-v5f      # پروگرام Firmware پرش و تصویر V5F
cargo xtask flash  --jump-v5f                      # پروگرام مجدد فقط jumper

cargo xtask merge  --example dualcore --dual-core  # تولید فایل‌های out/dualcore.bin و .hex
cargo xtask report --example dualcore              # خواندن و تفسیر mailbox همان نمونه
```

| حالت | هسته راه‌انداز V3F | هسته دوم V5F |
|---|---|---|
| حالت پیش‌فرض | بخش مربوط به نمونه انتخاب‌شده | بدون تغییر باقی می‌ماند |
| `--dual-core` | بخش نمونه انتخاب‌شده | بخش متناظر همان نمونه |
| `--jump-v5f` | برنامه عمومی jumper با نام `launcher` | بخش نمونه انتخاب‌شده |

حالت V5F-only وجود ندارد؛ چون پس از Reset فقط هسته راه‌انداز شروع به کار می‌کند. گزینه `--no-build` مرحله ساخت را رد می‌کند و `--out DIR` مسیر فایل‌های خروجی ادغام‌شده را تغییر می‌دهد. ادغام تصاویر به هر دو تصویر نیاز دارد؛ بنابراین باید از `--dual-core` یا `--jump-v5f` استفاده شود.

در عملیات Flash، ابتدا تصویر هسته راه‌انداز با گزینه `--no-run` نوشته می‌شود و تصویر آخر با نوشتن عادی پروگرام می‌شود؛ زیرا نوشتن تصویر نهایی باعث Reset و شروع اجرای تراشه خواهد شد.

## خواندن نتایج اجرا

هر نوع بازرسی با ابزار `wlink` هر دو هسته را متوقف می‌کند. نمونه‌های دو‌هسته‌ای نتایج اجرا را در mailbox مشترک قرار می‌دهند. برای نمونه:

```sh
cargo xtask flash --example cpuid --dual-core
cargo xtask report --example cpuid
```

بین دفعات خواندن گزارش فاصله بگذارید؛ ارسال پی‌درپی درخواست‌های `wlink` هر دو hart را در حالت توقف نگه می‌دارد و در نتیجه شمارنده‌ها ثابت به نظر می‌رسند. فرمان `run` از probe-rs استفاده می‌کند و خروجی RTT هسته V3F را نمایش می‌دهد. برای انتخاب یک پروگرامر مشخص از میان چند Probe، متغیر محیطی `PROBE_RS_PROBE=VID:PID:SERIAL` را تنظیم کنید.

اتصال RTT دو‌هسته‌ای برای اجرای پیوسته V5F هنوز اعتبارسنجی نشده است؛ برای اطمینان از اجرای hart 1 از mailbox و فرمان `report` استفاده کنید.

## معرفی نمونه‌ها

- **`blinky` و `sdi_print`:** نمونه‌های تک‌هسته‌ای هستند. `sdi_print` با وجود نام قدیمی، نمونه نمایش خروجی در کنسول RTT است؛ برای اجرا از `cargo xtask run --example sdi_print` استفاده کنید.
- **`i2c_scan` و `bme280_blocking`:** نمونه‌های تک‌هسته‌ای با رابط I2C1؛ پایه PB6 برای SCL و پایه PB7 برای SDA با عملکرد جایگزین AF4 استفاده می‌شوند. در این نمونه‌ها هسته راه‌انداز مستقیماً حسگر را می‌خواند.
- **نمونه‌های آزمون Peripheral:** شامل `adc_check`، `bus_check`، `can_async_check`، `can_check`، `dac_check`، `dma2_check`، `esig_check`، `exti_check`، `flash_check`، `rng_check`، `rtc_check`، `rtc_hse_check`، `spi_check`، `sdio_check` و `timer_check` هستند. این آزمون‌ها درایورهای ویژه H4 را بررسی می‌کنند و مقدارهای `stage` و `failures` را به‌همراه اطلاعات اختصاصی هر درایور در mailbox می‌نویسند. برای خواندن نتیجه از `cargo xtask report --example NAME` استفاده کنید. نمونه `can_check`، رابط CAN1 روی PA11/PA12 و CAN2 روی PB12/PB13 را در حالت Silent Loopback آزمایش می‌کند و به Transceiver خارجی نیاز ندارد. نمونه `can_async_check` همین آزمون را با درایور ناهمگام انجام می‌دهد و به قابلیت `embassy` نیاز دارد. نمونه `bus_check` نیز نمونه‌های USART، SPI و I2C فاقد آزمون مستقل را پوشش می‌دهد. آزمون RTC، منبع کلاک LSI و سپس LSE، وقفه‌های ثانیه و Alarm و سازوکار retargeting را بررسی می‌کند؛ `rtc_hse_check` از HSE/512 استفاده می‌کند.
- **`dma_m2m`، `delay_systick` و `embassy_systick`:** به‌ترتیب برای بررسی مسیر DMA/DMAMUX، تأخیر مسدودکننده SysTick و درایور زمان Embassy استفاده می‌شوند.
- **`eth_internal`:** پشته `embassy-net` را با PHY داخلی 10M/100M و نشانی ثابت `192.168.47.10/24` راه‌اندازی می‌کند. پورت J11 به میزبان موجود در شبکه `192.168.47.0/24` متصل می‌شود و نمونه به درخواست Ping پاسخ می‌دهد.
- **`eth_bench`:** همان پشته شبکه را به‌همراه پروتکل شمارش بایت نمونه `examples/ch32v467` روی TCP پورت 5001 اجرا می‌کند. برای اندازه‌گیری هر دو جهت انتقال می‌توان دستور `python3 ../ch32v467/eth_throughput_test.py 192.168.47.10` را اجرا کرد. نتیجه اندازه‌گیری‌شده برای بسته‌های ۸ مگابایتی، ۹۳ مگابیت‌برثانیه در ارسال و ۹۲ مگابیت‌برثانیه در دریافت بوده است؛ یعنی حدود ۹۸ درصد ظرفیت لینک 100BASE-TX. این نمونه از تنظیم کلاک ۱۲۰ مگاهرتز V3F و حلقه‌های DMA با عمق ۳۲ استفاده می‌کند. با اجرای همان درایور از Flash و اجرای hart 0، سرعت حدود ۵ مگابیت‌برثانیه بوده است.
- **`bme280`:** خواندن حسگر BME280 را به هسته دوم واگذار می‌کند. بخش V5F تابع `ch32_hal::init_secondary` را اجرا می‌کند، بدون آنکه درخت کلاک را تغییر دهد، رابط I2C1 را راه‌اندازی می‌کند و هر نمونه را در mailbox قرار می‌دهد. هسته راه‌انداز فقط داده را چاپ می‌کند. گزارش وضعیت حسگر (پس از پاسخ‌گویی تراشه مقدار `ok`) و آخرین مقادیر دما، فشار و رطوبت را نمایش می‌دهد.
- **`rcc_delay_check`:** آزمون رگرسیون تک‌هسته‌ای برای HSE با فرکانس پیش‌فرض ۲۵ مگاهرتز و تنظیم ۴۰۰ مگاهرتز است. فرکانس‌های ذخیره‌شده V3F/V5F، به‌روزرسانی RCC و مقادیر مقایسه SysTick برای تأخیرهای نانوثانیه‌ای گرد‌شده به بالا، تأخیر صفر و تأخیرهای دقیق میکروثانیه/میلی‌ثانیه را بررسی می‌کند. این نمونه hart 1 را بیدار نمی‌کند و خروجی چاپی ندارد. گزارش آن شامل `stage` (مقدار ۱ ورود به init، مقدار ۲ بازگشت از init و مقدار ۳ موفقیت آزمون‌ها)، `failures` (صفر به معنی موفقیت)، فرکانس‌های `v3f_hz` و `v5f_hz` (به‌ترتیب 100000000 و 400000000) و شمارنده یک‌ثانیه‌ای `heartbeat` است. گزارش را پس از هر بار پروگرام تازه یک‌بار بخوانید.
- **`cpuid`:** نمونه واگذاری کنترل بین دو هسته است. بخش V3F تابع `hal::init()` را برای راه‌اندازی RCC، تأخیر Flash، AFIO/GPIO و EXTI اجرا می‌کند، نشانی شروع hart 1 را در رجیستر بیدارباش PFIC می‌نویسد و سپس با دستور `wfi` متوقف می‌شود. بخش V5F رجیسترهای CSR مربوط به حالت Machine را خوانده و در mailbox قرار می‌دهد. مقدار `mhartid = 1` مشخص می‌کند داده‌ها از هسته دوم آمده‌اند.
- **`launcher`:** نسخه حداقلی واگذاری کنترل است و برای گزینه `--jump-v5f` به‌کار می‌رود. بلوک‌های مشترک را راه‌اندازی می‌کند، hart 1 را در نشانی تعیین‌شده توسط لینک‌کننده `v5f/memory.x` بیدار می‌کند و هسته V3F را متوقف می‌سازد. این برنامه یک برنامه Embassy نیست و Executor ندارد؛ تابع `hal::init()` فقط بلوک‌های موردنیاز بخش‌های V5F را تنظیم می‌کند.
- **`sdi_cpuid`:** نمونه شناسه CPU با نام قدیمی است. هر هسته در بلوک کنترل RTT مستقل خود گزارش ثبت می‌کند و V3F علاوه بر آن مقادیر V5F ذخیره‌شده در mailbox را نیز نمایش می‌دهد. فرمان `cargo xtask run` به ELF هسته V3F متصل می‌شود؛ برای جریان RTT مستقل هسته دوم باید از ELF مربوط به V5F استفاده شود. هر دو گزارشگر در حالت غیرمسدودکننده کار می‌کنند.
- **`atomics`:** هر دو هسته، متغیر مشترک را با عملیات `fetch_add` به تعداد یک میلیون بار افزایش می‌دهند؛ بنابراین مقدار نهایی باید دقیقاً ۲٬۰۰۰٬۰۰۰ باشد. مقدار کمتر نشان‌دهنده از دست رفتن به‌روزرسانی‌هاست. گزارش، مقدار نهایی، ماسک پایان کار و نتیجه را نمایش می‌دهد. هر دو Target JSON گزینه `"atomic-cas": true` را فعال کرده‌اند و هر دو crate قابلیت `qingke/unsafe-trust-wch-atomics` را دارند. Critical Section که وقفه‌های یک hart را غیرفعال می‌کند، به‌تنهایی نمی‌تواند دسترسی هم‌زمان دو هسته را سریال‌سازی کند.
- **`pingpong`:** هسته دوم رجیسترهای شناسه CPU خود را در mailbox ثبت می‌کند و هسته راه‌انداز آن‌ها را نمایش می‌دهد. سپس دو هسته در هر ثانیه یک دور تبادل انجام می‌دهند: hart 1 مقدار `ping` را افزایش می‌دهد، هسته راه‌انداز مقدار `pong` را ثبت می‌کند و hart 1 تا دریافت پاسخ منتظر می‌ماند. شمارنده‌ها را پس از اجرای فرمان Flash با `report` بخوانید.
- **`dualcore` و `hello`:** نمونه‌های حداقلی برای واگذاری کنترل و نخستین تبادل داده در حافظه مشترک هستند.

## تقسیم‌بندی Flash و RAM

تقسیم‌بندی حافظه مشابه CSDK شرکت WCH و فایل‌های `Ld/V3F/Link_v3f.ld` و `Ld/V5F/Link_v5f.ld` است:

| محدوده حافظه | مالک | توضیحات |
|---|---|---|
| `0x00000000` به طول ۶۴ کیلوبایت | تصویر بارگذاری V3F در Flash | مطابق `v3f/memory.x`؛ در شروع اجرا به RAM کپی می‌شود. |
| `0x20100000` به طول ۴۴۸ کیلوبایت | کد، داده و BSS هسته V3F | SRAM مشترک با دسترسی بدون Wait State |
| `0x20170000` به طول ۳۲ کیلوبایت | Stack هسته V3F | SRAM مشترک با دسترسی بدون Wait State |
| `0x00010000` به طول ۹۶۰ کیلوبایت منهای ۶۴ کیلوبایت | تصویر هسته V5F در Flash | مطابق `v5f/memory.x` |
| `0x20178000` به طول ۳۲ کیلوبایت | ناحیه مشترک `ipc::Mailbox` | در هر دو `memory.x` تعریف شده، اما فقط تصویر V3F آن را ایجاد می‌کند. |

هر تصویر محدوده حافظه مخصوص خود را تعریف می‌کند؛ بنابراین اگر اندازه خروجی از محدوده مجاز بیشتر شود، Linker خطا می‌دهد و تصویر دیگر را بازنویسی نمی‌کند. ابزار `xtask merge` نیز اندازه‌ها را دوباره بررسی می‌کند.

هسته راه‌انداز به‌طور کامل از بلوک حافظه مشترک اجرا می‌شود. hart 0 حافظه Instruction Cache قابل استفاده ندارد، حافظه Code FLASH دارای Wait State غیرصفر است (RM، بخش 1.2.1) و دسترسی hart 0 به ITCM هسته دوم دو چرخه HCLK تأخیر دارد؛ درحالی‌که بلوک ۵۱۲ کیلوبایتی SRAM مشترک برای هر دو هسته بدون Wait State است.

فایل `v3f/link.x` بخش‌های `.text` و `.rodata` را در `.highcode` قرار می‌دهد (با استفاده از قابلیت `highcode` در `qingke-rt`). در نتیجه فقط بخش‌های `.init` و `.handle_reset` در Flash باقی می‌مانند و کد در شروع کار به RAM منتقل می‌شود. اندازه‌گیری نمونه `eth_bench` نشان داده است که زمان یک حلقه یک‌میلیون‌تکراری محاسبات صحیح از ۱٫۱۲ ثانیه به ۸۷ میلی‌ثانیه کاهش می‌یابد و توان عملیاتی TCP از ۵ به ۹۳ مگابیت‌برثانیه در ارسال و ۹۲ مگابیت‌برثانیه در دریافت می‌رسد. بنابراین محدودیت ۶۴ کیلوبایت، محدودیت اندازه تصویر بارگذاری است، نه اندازه کدی که در زمان اجرا در RAM قرار می‌گیرد.

هسته V5F کد را مستقیماً از Flash اجرا می‌کند و دارای Instruction Cache است؛ بنابراین به کپی کد نیاز ندارد. کپی ITCM در CSDK برای hart 1 در این پروژه پیاده‌سازی نشده است؛ در نتیجه اندازه بخش‌های V5F باید کوچک نگه داشته شود.

**نکات مهم هنگام تغییر تقسیم‌بندی حافظه:**

- بخش عمده RAM رزروشده برای V3F هنوز خالی است. اندازه `.text` و `.rodata` هسته V3F حدود ۶۳ کیلوبایت و `.bss` آن حدود ۱۶۹ کیلوبایت است؛ بنابراین بخشی از ناحیه ۴۴۸ کیلوبایتی آزاد می‌ماند. این فضا برای توسعه آتی V3F رزرو شده است. اگر V5F به بافر مشترک بین دو هسته نیاز پیدا کند، می‌توان محدوده `RAM` را کوچک‌تر و `STACK` را به نشانی پایین‌تری منتقل کرد؛ اما مجموع سه ناحیه باید همچنان ۵۱۲ کیلوبایت باشد و `SRAM_SHARED` باید در `0x20178000` باقی بماند؛ زیرا هر دو فایل `memory.x` و ثابت `ipc::MAILBOX_ADDR` به این نشانی وابسته‌اند.
- ناحیه ۶۴ کیلوبایتی فقط محدودیت تصویر بارگذاری است و نمونه `eth_bench` حدود ۶۳ کیلوبایت آن را مصرف می‌کند. بزرگ‌تر شدن تصویر V3F از این مقدار باعث خطای Linker می‌شود. برای افزایش آن می‌توان `CODE` و `USR` را به ۱۲۸ کیلوبایت گسترش داد و آغاز تصویر V5F را به `0x00020000` منتقل کرد. این تغییر به اصلاح `v3f/memory.x`، `v5f/memory.x`، ساختار `Core` در `xtask/src/main.rs` و جدول حافظه همین مستند نیاز دارد.
- فایل `v3f/link.x` نسخه محلی‌شده اسکریپت `qingke-rt` است و یک قابلیت upstream محسوب نمی‌شود. فقط عملیات کپی `highcode` از `qingke-rt` می‌آید؛ قراردادن `.text` و `.rodata` در `.highcode` و قرار دادن جدول بردارها در ابتدای این بخش، تغییر اختصاصی این پروژه است. در صورت تغییر چیدمان حافظه در نسخه‌های بعدی `qingke-rt` باید این فایل نیز دستی هماهنگ شود.

### Mailbox؛ حافظه تبادل اطلاعات دو هسته

ناحیه مشترک `SRAM_SHARED` از نشانی `0x20178000` و به طول ۳۲ کیلوبایت، یک شیء مشترک از نوع `ch32h417_ipc::Mailbox<T>` را نگهداری می‌کند. این شیء شامل یک کلمه نسخه‌بندی ساختار و Payload اختصاصی نمونه با ویژگی `#[repr(C)]` است.

نوع Payloadها در فایل `ipc/src/protocols.rs` تعریف شده‌اند؛ برای هر نمونه یک ساختار مستقل وجود دارد. در نتیجه هر نمونه فقط فیلدهای موردنیاز خود را رزرو می‌کند و نمونه‌های مختلف نیز از فیلد مشترک یکسان استفاده نمی‌کنند.

شیء mailbox فقط توسط هسته راه‌انداز ایجاد و تعریف می‌شود؛ هسته دوم نباید نمونه دیگری از آن را ایجاد کند:

```text
تصویر V3F: بخش .shared از نوع NOLOAD، نماد MAILBOX در 0x20178000 را تعریف می‌کند.
تصویر V5F: بخش .shared و نماد MAILBOX ندارد و از ipc::at(MAILBOX_ADDR) استفاده می‌کند.
```

نشانی mailbox بین دو تصویر مذاکره نمی‌شود. فایل `shared.x` بخش `.shared` را در ابتدای `SRAM_SHARED` قرار می‌دهد؛ هر دو `memory.x` این ناحیه را روی `0x20178000` ثابت می‌کنند و `ch32h417_ipc::MAILBOX_ADDR` نیز همان نشانی را مشخص می‌کند. بنابراین تغییر این نشانی مستلزم اصلاح هر سه محل است.

فرمان `cargo xtask report --example NAME` پروتکل ذخیره‌شده توسط همان نمونه را رمزگشایی می‌کند. طول داده خوانده‌شده برابر `size_of::<Mailbox<ThatProtocol>>()` است. چون این ناحیه از نوع `NOLOAD` است، فضایی در Flash اشغال نمی‌کند و در زمان راه‌اندازی نیز به‌صورت خودکار صفر نمی‌شود؛ بنابراین اگر یکی از هسته‌ها Reset شود، داده‌ها می‌توانند درحالی‌که هسته دیگر مشغول اجراست باقی بمانند. هسته راه‌انداز پیش از واگذاری کنترل، mailbox را پاک می‌کند تا گزارش مربوط به اجرای فعلی باشد. کلمه نسخه فقط نسخه Container را مشخص می‌کند و به دو تصویر که جداگانه پروگرام شده‌اند امکان می‌دهد ناسازگاری نسخه را تشخیص دهند.

## نکات فنی

- **سطح دسترسی پردازنده:** نسخه `qingke-rt` از 0.8.2 به بعد، تابع `main` را در حالت Machine اجرا می‌کند. به همین دلیل V3F می‌تواند مستقیماً CSRهایی مانند `misa` و `mhartid` را بخواند. فعال‌کردن قابلیت `u-mode` رفتار راه‌اندازی WCH را به حالت User بازمی‌گرداند؛ در این حالت فقط CSRهای مجاز `URW` مانند `gintenr` و `intsyscr` قابل دسترسی هستند.
- **کامل نبودن بعضی عملیات نوشتن Flash با wlink:** ممکن است `wlink flash` موفقیت را اعلام کند، درحالی‌که فقط بخشی از تصویر نوشته شده باشد. به همین دلیل `xtask` تصویرهایی را که با `--no-run` نوشته شده‌اند دوباره می‌خواند و در صورت عدم تطابق، عملیات نوشتن را تکرار می‌کند. نوشتن تصویر نهایی باعث Reset و اجرای تراشه می‌شود و به همین روش قابل بررسی نیست. اگر در نمونه دو‌هسته‌ای فقط فعالیت V3F دیده شد، پیش از مشکوک شدن به Firmware یک بار دیگر پروگرام کنید. گزینه `--flasher probe-rs` جایگزین این روش است؛ زیرا هر تصویر را با probe-rs می‌نویسد و بررسی می‌کند و در پایان فقط یک Reset انجام می‌دهد. برای انتخاب بین چند Probe باید `PROBE_RS_PROBE` تنظیم شود. حالت پیش‌فرض همچنان `wlink` است که اولین Probe را انتخاب می‌کند.
- **تصاویر ادغام‌شده:** فایل `out/NAME.bin` تصویر کامل Flash است و بین دو Payload با مقدار `0xFF` پر می‌شود. فایل `out/NAME.hex` همان تصویر را به شکل رکوردهای HEX نمایش می‌دهد که فقط محدوده Payloadها را پوشش می‌دهند. برای انتشار از تصویر ادغام‌شده و برای چرخه توسعه از مسیر `xtask flash` با دو ELF استفاده کنید.
- **گزینه xwchc:** هر دو Target Spec مربوط به V3F و V5F این قابلیت را فعال کرده‌اند. این گزینه از دستورهای فشرده بارگذاری و ذخیره Byte و Halfword مخصوص WCH استفاده می‌کند که هر دو هسته QingKe V3 و V5 پشتیبانی می‌کنند (راهنمای QingKe V3، جدول 1-1). این گزینه فقط بر تولید کد اثر می‌گذارد؛ بنابراین CI مربوط به HAL با Targetهای استاندارد ساخته می‌شود و فعال‌سازی آن به نمونه‌ها واگذار شده است.
- **Instruction Cache هسته دوم:** حافظه نهان دستور hart 1 پس از Reset غیرفعال است (رجیستر `cache_strtg_ctlr`، شماره CSR برابر `0xBC2` و بیت `ic_disable = 1`). بنابراین هر بخش V5F باید در ابتدای اجرا `ch32h417_v5f::cache::enable_icache()` را فراخوانی کند. هسته hart 0 حافظه نهان دستور قابل استفاده ندارد؛ به همین علت تصویر V3F از SRAM مشترک اجرا می‌شود و تنظیم آن در `v3f/link.x` با قابلیت `highcode` انجام شده است.

## نمونه‌های فایل‌سیستم کارت SD

نمونه `sdio_fat` اولین پارتیشن MBR را در صورت FAT16 یا FAT32 بودن انتخاب می‌کند. نمونه `sdio_exfat` نیز اولین پارتیشن MBR با فایل‌سیستم exFAT را به‌کار می‌گیرد. هر دو نمونه از پایه‌های PB11 و PB10 و پایه‌های PE8 تا PE11 به‌همراه کانال ۱ از DMA1 استفاده می‌کنند.

فرکانس درخواستی SDIO برابر ۲٫۵ مگاهرتز است؛ زیرا فرکانس‌های بالاتر روی برد H417 و کارت مورد استفاده هنوز پایداری کافی ندارند. این نمونه‌ها به‌صورت Blocking اجرا می‌شوند.

در هر بار اجرا، فایل جدیدی با نام‌های متوالی `H4TST00.TXT` تا `H4TST99.TXT` ساخته می‌شود. برنامه ۱۳۰۰ بایت در فایل می‌نویسد، فایل‌سیستم را Unmount و دوباره Mount می‌کند و سپس محتوا را بررسی می‌کند. نام‌های موجود رد می‌شوند و فایل آزمون روی کارت باقی می‌ماند. هنگام اجرای این نمونه‌ها از کارتی استفاده نکنید که هم‌زمان روی میزبان دیگری Mount شده است.

```text
cargo xtask flash --example sdio_exfat
cargo xtask report --example sdio_exfat
```

در هر یک از دو نمونه، مقادیر `stage = 7`، `error = 0` و `bytes verified = 1300` نشان‌دهنده موفقیت هستند. مقدار `test file number = 1` به معنی ایجاد فایل `H4TST01.TXT` است. مقدار خطای غیرصفر یا توقف در مرحله‌ای پیش از ۷ به معنی کامل نشدن عملیات فایل‌سیستم است. نمونه FAT به پارتیشن FAT16/FAT32 نیاز دارد و نمونه exFAT کارت را فرمت نمی‌کند.

---

</div>

## Folder Overview

Dual-core CH32H417 examples covering V3F/V5F firmware, inter-core communication (IPC), xtask utilities, and peripheral tests.

---

# CH32H417 examples

CH32H417 is one chip with **two RISC-V cores**, so these examples are organised
per chip rather than per core:

```
ch32h417/
├── v3f/     control core, hart 0 — riscv32imafc, flash 0x00000000…0x0000FFFF
├── v5f/     second core,  hart 1 — riscv32imafbc, flash 0x00010000…
├── ipc/     the shared mailbox, consumed by both crates
├── xtask/   build / flash / run / merge / report driver
└── out/     merged images (generated)
```

`v3f/` and `v5f/` are separate crates: different ISA (`imafc` vs `imafbc`),
different ABI, different `memory.x`. They share one flash chip and one mailbox
in shared RAM, which `xtask` handles.

## Two kinds of example

Reset only starts the **V3F**. The V5F stays put until the V3F writes its wake
register (`PFIC_WAKEIP1`), so examples come in exactly two shapes, which
`xtask` derives from the files that exist. A `v5f/src/bin/NAME.rs` without a
`v3f` half is rejected.

| Kind | Files | Examples |
|---|---|---|
| **v3f-only** | `v3f/src/bin/NAME.rs` | `adc_check`, `blinky`, `bme280_blocking`, `bus_check`, `can_async_check`, `can_check`, `dac_check`, `delay_systick`, `dma2_check`, `dma_m2m`, `embassy_systick`, `esig_check`, `exti_check`, `flash_check`, `i2c_scan`, `launcher`, `pioc_text`, `pioc_ws2812`, `rcc_delay_check`, `rng_check`, `rtc_check`, `rtc_hse_check`, `sdi_print`, `sdio_check`, `sdio_exfat`, `sdio_fat`, `spi_check`, `timer_check` |
| **dual-core** | both `src/bin/NAME.rs` | `atomics`, `bme280`, `cpuid`, `dualcore`, `hello`, `pingpong`, `sdi_cpuid` |

## PIOC examples

`pioc_ws2812` and `pioc_text` drive an 8x8 WS2812 panel from WCH's RGB1W
firmware, which runs inside the PIOC instead of the RISC-V core. Neither builds
without `--features embassy`, and both need the HAL's manual `pioc` feature,
which the crate already enables.

- The panel accepts IO0 on four pins. `pioc-io0-pe3` is the default here because
  it is a 3.3 V pad and is routed out on the boards at hand; `pioc-io0-pf12` is
  a trap, since PF11-PF14 sit in the VIO18 domain and only swing 1.8 V.
- `v3f/src/panel.rs` holds the chain layout (`RowZigzag`, mirrored) and the
  colour wheel both examples share. A wrong layout mirrors or shuffles the
  picture, and only an asymmetric test image such as text shows it.
- `firmware/rgb1w-100m.pio` is `EXAM/PIOC/PIOC_1_Wire/Common/Asm/RGB1W.ASM`
  translated to the canonical dialect; assembling it reproduces the vendor
  `RGB1W.BIN` byte for byte. The delay loops are `nop` padding, so the image is
  tied to the 100 MHz PIOC clock the panel is driven at here.

In a dual-core example the V3F half brings the chip up (clocks, GPIO),
hands over to hart 1, and reports. The V5F half depends on `ch32-hal` but never
calls `ch32_hal::init`: that re-programs the PLL and then blocks on
`CFGR0.SWS == PLL`, which would switch SYSCLK out from under the core that is
running. It calls `ch32_hal::init_secondary` instead, which only reads the clock
tree the boot core already programmed back into `rcc::clocks()`.
`ch32h417_v5f::pac` re-exports the HAL's metapac. Each image has its own
non-blocking defmt RTT buffer. The two cores still must not both use one peripheral: each
image has its own `Peripherals` singleton, so neither can tell that the other
already took it.

## Commands

```sh
cd examples/ch32h417

cargo xtask build  --example cpuid                 # boot core only (the default)
cargo xtask build  --example cpuid --dual-core     # both of its halves
cargo xtask build  --example cpuid --jump-v5f      # jumper + cpuid's V5F half
cargo xtask build                                  # everything

cargo xtask flash  --example dualcore --dual-core  # build + write both, no console
cargo xtask flash  --example eth_bench --flasher probe-rs  # verify with probe-rs instead
cargo xtask run    --example dualcore --dual-core  # same, plus the V3F RTT console
cargo xtask flash  --example cpuid --jump-v5f      # jump firmware + V5F payload
cargo xtask flash  --jump-v5f                      # reflash the jumper alone

cargo xtask merge  --example dualcore --dual-core  # out/dualcore.bin + .hex
cargo xtask report --example dualcore              # decode that example's mailbox
```

| Layout | Boot core (`v3f`) | Second core (`v5f`) |
|---|---|---|
| *(default)* | the named example's half | left exactly as it is |
| `--dual-core` | the named example's half | the named example's half |
| `--jump-v5f` | the generic jumper, `launcher` | the named example's half |

There is no V5F-only mode: reset only starts the boot core, so a V5F payload
would have nothing to run. `--no-build` skips the build step, `--out DIR` moves
the merged artifacts. Merging needs both images, so it asks for `--dual-core`
or `--jump-v5f`. Flashing writes the boot core first with `--no-run` and the
last image with a plain write, because the final write is the one that resets
and runs the chip.

## Reading results back

Any `wlink` inspection halts both cores. For dual-core execution results the
examples publish into the shared mailbox:

```sh
cargo xtask flash --example cpuid --dual-core
cargo xtask report --example cpuid
```

Leave a gap between reads: a burst of `wlink` requests keeps both harts halted,
so counters look frozen. `run` uses probe-rs and shows V3F RTT output. For multiple
probes, set `PROBE_RS_PROBE=VID:PID:SERIAL`. Dual-core RTT attach has not been
validated for continuous V5F execution; use the mailbox to verify hart 1.

## The examples

- **`blinky`**, **`sdi_print`** — v3f-only. `sdi_print` is the RTT console demo (legacy name):
  `cargo xtask run --example sdi_print`.
- **`i2c_scan`**, **`bme280_blocking`** — v3f-only, I2C1 on PB6 = SCL, PB7 = SDA
  (AF4). The boot core reads the sensor itself.
- **`adc_check`**, **`bus_check`**, **`can_async_check`**, **`can_check`**,
  **`dac_check`**, **`dma2_check`**, **`esig_check`**, **`exti_check`**,
  **`flash_check`**, **`rng_check`**, **`rtc_check`**, **`rtc_hse_check`**,
  **`spi_check`**, **`sdio_check`**, **`timer_check`** — v3f-only self-checks
  for the H4-only drivers. Each
  publishes a `stage` and a `failures` count into the mailbox (plus
  driver-specific fields); read them with
  `cargo xtask report --example NAME`. `can_check` runs CAN1 on PA11/PA12 and
  CAN2 on PB12/PB13 in silent loopback, so no transceiver is needed;
  `can_async_check` repeats it through the async driver and needs the
  `embassy` feature (xtask builds it with that feature). `bus_check` covers
  the USART/SPI/I2C instances that have no other check. `rtc_check` runs the
  RTC from LSI and then LSE, exercising the second/alarm interrupts and the
  retargeting guard; `rtc_hse_check` boots the HSE recipe and uses HSE/512.
- **`dma_m2m`**, **`delay_systick`**, **`embassy_systick`** — bring-up checks
  for the DMA/DMAMUX path, the blocking SysTick delay and the Embassy time
  driver.
- **`eth_internal`** — `embassy-net` over the integrated 10M/100M PHY with the
  static address `192.168.47.10/24`; the J11 port goes to a host on
  `192.168.47.0/24`. It answers ping.
- **`eth_bench`** — the same stack plus the `examples/ch32v467` byte-count
  protocol on TCP 5001, so `python3 ../ch32v467/eth_throughput_test.py
  192.168.47.10` measures both directions. Measured 93 Mbit/s up and 92 Mbit/s
  down over 8 MiB rounds — 98% of what a 100BASE-TX link carries. It runs the
  120 MHz V3F recipe with 32-deep DMA rings; with the same driver while hart 0
  still executed from flash it was 5 Mbit/s.
- **`bme280`** — the same sensor read by hart 1 instead. The V5F half calls
  `ch32_hal::init_secondary` (which reads the clock tree back rather than
  touching it) and brings up I2C1 itself, then stores each sample in the
  mailbox; the boot core only prints it. `report` prints the
  sensor state (`ok` once the chip answered) and the last temperature, pressure
  and humidity.
- **`rcc_delay_check`** — v3f-only regression check for the default 25 MHz HSE at
  the 400 MHz preset: cached V3F/V5F frequencies, RCC refresh, and SysTick
  compare values for rounded-up nanosecond delays, zero delay, and exact
  microsecond/millisecond delays. It does not wake hart 1 or print.
  `report` reads its own payload: `stage` (1 = init entered, 2 = init returned,
  3 = checks passed), `failures` (**0** means success), `v3f_hz` / `v5f_hz`
  (**100000000 / 400000000**) and `heartbeat` (one-second counter). Read once per
  fresh flash.
- **`cpuid`** — the handover example. The V3F half runs `hal::init()` (RCC, flash
  latency, AFIO/GPIO, EXTI), writes hart 1's entry to the PFIC wake register and
  parks in `wfi`; the V5F half probes its own Machine-mode CSRs into the mailbox.
  `mhartid = 1` is what shows the values came from the second core.
- **`launcher`** — the same handover with nothing else, used by `--jump-v5f`: it
  brings the shared blocks up, wakes hart 1 at the address `v5f/memory.x` links
  it to, and parks the V3F. Not an embassy application — no executor, and
  `hal::init()` only touches the blocks the V5F halves depend on.
- **`sdi_cpuid`** — the legacy-named CPU-ID example. Each core logs to its own
  RTT control block, and V3F also logs the V5F values published in the mailbox.
  `cargo xtask run` attaches to the V3F ELF; use the V5F ELF for its separate
  RTT stream. Both loggers use non-blocking mode.
- **`atomics`** — both cores `fetch_add` the same shared word `CAS_INCREMENTS`
  (1,000,000) times each, so the total must be exactly 2,000,000. Anything less
  means updates were lost. `report` prints the total, the done mask and the
  verdict. Both target JSONs set `"atomic-cas": true` and both crates enable
  `qingke/unsafe-trust-wch-atomics`; a critical section that masks one hart's
  interrupts cannot serialise two cores.
- **`pingpong`** — hart 1 probes its CPU-ID CSRs into the mailbox, the boot core
  prints what hart 1 reported, and from then on the two exchange one round per
  second: hart 1 increments `ping`, the boot core stores `pong`, and hart 1
  waits for the answer before the next round. Read the counters with `report`
  after a `flash`.
- **`dualcore`**, **`hello`** — the minimal handover and the first shared-memory
  exchange.

## Flash / RAM partition

The same split as the WCH CSDK (`Ld/V3F/Link_v3f.ld`, `Ld/V5F/Link_v5f.ld`):

| Region | Owner | Notes |
|---|---|---|
| `0x00000000` + 64K | V3F load image (flash) | `v3f/memory.x`; copied to RAM at startup |
| `0x20100000` + 448K | V3F code, data, bss | zero-wait shared SRAM |
| `0x20170000` + 32K | V3F stack | zero-wait shared SRAM |
| `0x00010000` + 960K−64K | V5F image (flash) | `v5f/memory.x` |
| `0x20178000` + 32K | shared region (`ipc::Mailbox`) | declared by both `memory.x`; defined by the V3F image only |

Either image declares its own range, so an oversized build fails to link
instead of overwriting the other image; `xtask merge` checks the sizes again.

The boot core runs entirely from the shared block. Hart 0 has no usable
instruction cache, Code FLASH is non-zero-wait (RM §1.2.1), and hart 1's ITCM
costs hart 0 two HCLK waits — while the 512 KB shared block is zero-wait for
both cores. `v3f/link.x` folds all of `.text`/`.rodata` into `.highcode` (the
`qingke-rt` `highcode` feature), so only `.init` and `.handle_reset` stay in
flash and the startup copies the rest into RAM. Measured with `eth_bench`: a
1M-iteration integer loop drops from 1.12 s to 87 ms, and TCP throughput from
5.0 to 93 Mbit/s up, 92 Mbit/s down. The 64 KB flash slot is now a load-image
limit, not an execution one.

The V5F executes from flash in place and has an instruction cache, so it does
not need the copy; the CSDK's ITCM copy for hart 1 is not implemented here, so
keep V5F halves small.

Three things about that split are worth knowing before changing it:

- **Most of `RAM` is slack.** The V3F's own `.text`/`.rodata` is 63 KB and its
  `.bss` 169 KB, so roughly half of the 448 KB region is unused. It is reserved
  for V3F growth; if a V5F payload ever needs a cross-core buffer, shrink `RAM`
  and move `STACK` down instead — the three regions must still add up to
  512 KB, and `SRAM_SHARED` has to stay at `0x20178000`, because both
  `memory.x` files and `ipc::MAILBOX_ADDR` name that address.
- **The 64 KB slot is only a load-image limit**, and `eth_bench` fills 63 KB of
  it. A V3F image that outgrows it is a link error; the way out is to widen
  `CODE`/`USR` to 128 KB and start the V5F image at `0x00020000`, which touches
  `v3f/memory.x`, `v5f/memory.x`, `Core` in `xtask/src/main.rs` and this table.
- **`v3f/link.x` is a local copy of `qingke-rt`'s script**, not an upstream
  feature. Only the `highcode` copy itself comes from `qingke-rt`; folding
  `.text`/`.rodata` into `.highcode` and starting that section with the vector
  tables is this crate's addition, so keep it in sync by hand when `qingke-rt`
  changes its layout.

### Mailbox

One shared region, `SRAM_SHARED` (`0x20178000` + 32K), holding one shared
object: `ch32h417_ipc::Mailbox<T>`, a layout-version word plus the example's own
`#[repr(C)]` payload. The payload types live in `ipc/src/protocols.rs` — one per
example, so an example no longer reserves words for protocols it never runs, and
two examples no longer share a field. The boot core **defines** the object; the
second core never places one:

```
v3f image   .shared (NOLOAD) -> MAILBOX @ 0x20178000   (defined here, and only here)
v5f image   no .shared, no MAILBOX symbol -> ipc::at(MAILBOX_ADDR)
```

The address is not negotiated between the images. `shared.x` puts `.shared` at
the start of `SRAM_SHARED`, both `memory.x` files pin that region at
`0x20178000`, and `ch32h417_ipc::MAILBOX_ADDR` names the same place, so moving
the region means changing all three. `cargo xtask report --example NAME` decodes
whichever protocol that example stores there; the read length is
`size_of::<Mailbox<ThatProtocol>>()`. Because the region is `NOLOAD` it costs no
flash and nothing zeroes it at boot, so it survives one core resetting while the
other runs. The boot core clears it before handing over, so `report` describes
the current run. The version word versions the container only, so two
independently flashed halves can notice they disagree about it.

## Notes

- **Privilege.** `qingke-rt` ≥ 0.8.2 leaves `main` in Machine mode, which is what
  lets the V3F read `misa`, `mhartid` and friends directly. The `u-mode` feature
  restores WCH's startup behaviour (User mode), where only the `URW` CSRs
  `gintenr`/`intsyscr` remain reachable.
- **Flash writes are not always complete with `wlink`.** `wlink flash` can
  report success while only part of the image landed, so `xtask` reads every
  `--no-run` image back and repeats the write if it did not. The final write
  resets and runs the chip, so it cannot be checked the same way — if a
  dual-core example only shows V3F activity, flash it again before suspecting
  the firmware. `--flasher probe-rs` avoids this: it writes every image with
  probe-rs, which verifies each one, and issues a single reset at the end. It
  cannot choose between two attached probes, so set `PROBE_RS_PROBE` there; the
  default stays `wlink`, which picks the first probe by itself.
- **Merged images.** `out/NAME.bin` is the full flash image (`0xFF` padding
  between the two payloads); `out/NAME.hex` is the same image as records, which
  only cover the payloads. Use the merged image to publish and the two-ELF
  `xtask flash` path to iterate.
- **`xwchc`.** Both target specs (`v3f/riscv32imafc-unknown-none-elf.json`,
  `v5f/riscv32imafbc-unknown-none-elf.json`) enable it: WCH's compressed byte and
  halfword loads and stores, which QingKe V3F and V5F both implement (QingKe V3
  manual, table 1-1). It only changes codegen, so the HAL's CI builds against the
  stock targets and leaves it to the examples.
- **Hart 1's instruction cache is off out of reset** (`cache_strtg_ctlr`, CSR
  `0xBC2`, `ic_disable = 1`), so every V5F half starts with
  `ch32h417_v5f::cache::enable_icache()`. Hart 0 has no usable instruction cache,
  so the V3F image runs from the shared SRAM (`v3f/link.x`, `highcode`).

## SD card file-system examples

`sdio_fat` uses the first MBR partition when it is FAT16/FAT32; `sdio_exfat`
uses the first MBR partition when it is exFAT. Both use PB11/PB10 and PE8–PE11
with DMA1 channel 1. They request 2.5 MHz: higher SDIO clock settings on the
current H417 board and card are not yet reliable. These are blocking examples.

Each run creates a new file named `H4TST00.TXT` through `H4TST99.TXT`, writes
1300 bytes, remounts, and verifies the contents. Existing filenames are skipped;
the test file is left on the card. Do not run these examples against a mounted
card shared with another host.

```text
cargo xtask flash --example sdio_exfat
cargo xtask report --example sdio_exfat
```

For either example, `stage = 7`, `error = 0`, and `bytes verified = 1300`
indicate success; `test file number = 1` means `H4TST01.TXT`. A nonzero error
or an earlier stage means the file-system operation did not complete. The
FAT16/FAT32 example requires a FAT16/FAT32 partition; the exFAT example does
not format the card.

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
