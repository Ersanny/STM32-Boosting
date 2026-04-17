# STM32-Boosting

## Eğitim Hedefi
- Sıfırdan İnşa : Hiçbir ön koşul veya hazır şablon yok.

- Register Seviyesinde Kodlama: STM32 kütüphaneleri (HAL veya Standard Peripheral Library) KULLANILMAYACAK. Kodlama doğrudan işlemcinin en alt bellek seviyesi olan register'lara müdahale edilerek yapılacak.

- Dokümantasyon Odaklılık: Kodlar internetten kopyalanmayacak. Temel referans kaynakları Datasheet ve Reference Manual olacak. Amaç, bir MCU manuelinin nasıl okunup yorumlanacağını öğrenmek.

- Kendi Kütüphanemizi Yazmak: İşlemciyi anladıktan sonra, gelecekteki projelerde kullanmak üzere kendi modüler kütüphanelerimizi sıfırdan yazacağız.

- Zamanlama Analizi : Sadece "çalışan" kod değil, donanımlar arası kısıtları ve zamanlama gereksinimlerini karşılayan, saat vuruşlarına (clock) optimize edilmiş kodlar hedefleniyor.

## IDE Seçimi
- Keil uVision: Neden? Sadece STM32'yi değil, ARM mikroişlemci mimarisini genel olarak öğrenmek için. Keil, ARM tabanlı sistemler için endüstri standartlarındandır. Bu yetenek diğer MCU ailelerine geçişi kolaylaştırır.

- STM32CubeMX: Kullanım Amacı, Kod üretmek (code generation) için KULLANILMAYACAK. Stratejik Rolü, Sadece MCU'nun iç mimarisini, Timer yapılarını ve özellikle karmaşık Clock ağacını (Clock Tree) görsel olarak anlamak ve eğitim amaçlı analiz yapmak için kullanılacak.

## Eğitim Metodu
- Temel Hazırlık (Foundational Part): Arka planda neler döndüğünü anlama, binary iletişim, Keil kurulumu ve ortam hazırlığı.

- Kodlama ve PoC (Proof of Concept): Temel seviyede kodu yazıp donanımın çalıştığını kanıtlama.

- Modüler Fonksiyonlaştırma: Sadece tek bir projede değil, tüm STM32 ailesinde çalışabilecek modüler fonksiyonlar yazma. (GPIO, Timers, ADC, SPI, UART, I2C, DMA işlemleri).

- Proje Entegrasyonu: Yazılan fonksiyonları efektif bir şekilde gerçek dünya projelerinde birleştirme.

Not: Saat Frekansı (Clock Frequency): Kartlar genelde varsayılan olarak 16 MHz'de başlar. Bu işlemcilerin tüm gücünü (maksimum frekanslarını) sistemi stabil tutarak nasıl serbest bırakacağımızı (unleash) öğrenilecek.
