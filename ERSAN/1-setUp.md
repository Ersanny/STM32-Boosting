# Keil uVision Kurulumu ve Bare-Metal Proje Mimarisi

## Geliştirme Ortamı Seçimi: Neden Keil uVision?

- Gömülü sistemler dünyasında birçok popüler alternatif olmasına rağmen stratejik olarak ARM tabanlı Keil (MDK-ARM) seçilmiştir. Alternatiflerin elenme nedenleri şunlardır:

- Arduino IDE (Elenen): Çok temeldir. Arka planda donanım ile yazılım arasında ne olduğunu tamamen gizler, donanım mimarisinin anlaşılmasını engeller.

- STM32CubeIDE / ST Software (Elenen): Mükemmel araçlar olmalarına rağmen sadece STMicroelectronics ürünlerine kilitlidir (Vendor Lock-in).

- MicroPython (Elenen): C dilinin donanım üzerindeki mutlak kontrolünden uzaktır. Derleme katmanında neler olup bittiği belirsizdir, verimsizliklere (inefficiencies) yol açabilir.

- Keil uVision (SEÇİLEN): Doğrudan işlemcileri üreten ARM'ın kendi yazılımıdır. Sadece STM32'yi değil, 20'den fazla üreticinin 9000'den fazla mikrodenetleyicisini (MCU) destekler. Keil'i öğrenmek, tüm ARM ekosistemini (NXP, Microchip, Nuvoton vb.) programlayabilmek demektir.

## Keil'in Avantajları (All-in-One IDE)

- Harici programlara ihtiyaç duymadan gömülü geliştirme döngüsünün tüm adımlarını tek platformda çözer:

- Text Editor: Kod yazımı.

- Assembler: Yazılan C kodunu anında makine diline (Assembly) çevirir (İşlemcinin ne anladığını görmek için kritik).

- Compiler: Yazılımı derler.

- Loader (Flasher): Kodu doğrudan MCU'nun içine yükler.

- Debugger: (En önemli özellik) Donanım üzerinde kodu satır satır çalıştırmayı (step-by-step), değişkenleri ve bellek (register) durumlarını canlı olarak okumayı sağlar.

- Simulator: Gerçek donanım elde olmasa bile sanal ortamda işlemciyi simüle edip kodun davranışını test etmeye olanak tanır.

## İLK PROJE: Bare-Metal Kurallarına Göre Proje Başlatma (KRİTİK)

- Yeni proje (Project -> New uVision Project) oluşturup hedef MCU (Örn: STM32F407VG) seçildikten sonra ekrana gelen "Manage Run-Time Environment" penceresi

- Hazır kütüphaneler (HAL, SPL vb.) SEÇİLMEYECEKTİR. Sistemde sadece işlemcinin ayağa kalkması için sadece gereken minimum dosyalar seçilmelidir:

- CMSIS -> CORE: Seçilecek. (ARM Cortex-M çekirdeği ile temel iletişim, kesmeler (interrupts) ve temel işlemci özellikleri için).

- Device -> Startup: Seçilecek. (Sistemin başlangıç dosyasıdır. Reset sonrası ilk çalışan Assembly kodlarını, vektör tablosunu ve ana saatin (clock) temel ilk yüklemesini barındırır).

- Sadece bu ikisi seçildiğinde, dışarıdan hiçbir "abstraction" (soyutlama) kütüphanesi projemize bulaşmamış olur. Kalan her şeyi Datasheet okuyarak biz yazacağız.

## Dokümantasyon Erişimi

- Keil'in "Books" (Kitaplar/Dokümantasyon) sekmesi, seçilen MCU'ya ait Reference Manual ve Datasheet belgelerini doğrudan IDE içinden çevrimdışı (offline) olarak açma imkanı sunar. Sürekli web tarayıcısına gitme ihtiyacını ortadan kaldırır.
