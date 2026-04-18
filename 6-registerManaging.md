# STM32 Donanım Mimarisi, Bus (Veriyolu) Sistemleri ve C Struct İle Hafıza Erişimi

## Datasheet vs. Reference Manual Nedir?
STMicroelectronics dokümantasyonlarında kaybolmamak için bu iki belgenin farkı kesin olarak bilinmelidir:

- Datasheet : Mikrodenetleyicinin "Katalog" veya "Vitrin" bilgileridir. 
- İşlemci hızı (180 MHz), 
- Flash/RAM boyutları (512KB/128KB), 
- elektriksel limitler ve pinlerin fiziksel yerleşimlerini (Pinout) içerir. Genel mimari diyagramına buradan bakılır.

- Reference Manual : İşlemcinin "Kullanım Kılavuzu" ve "İç Organları"dır. 
- Register adresleri, bitlerin tam olarak ne işe yaradığı ve çevrebirimlerinin (Timer, SPI, ADC) nasıl programlanacağı sadece bu belgede yazar. Kod yazarken asıl rehberimiz budur.

### İşlemci Anatomisi: Highway (AHB) ve Local Roads (APB)
Mikrodenetleyici içindeki her şey birbirine "Bus" (Veriyolu) adı verilen dijital yollarla bağlıdır. 32-bitlik bir ARM işlemcide bu yollar 32 şeritli bir otoyol gibidir (Her saat vuruşunda 32 bit veri taşınır).

#### STM32F4 mimarisi temel olarak 2 hız bölgesine ayrılır:

- AHB (Advanced High-performance Bus): İşlemcinin ana otoyoludur. İşlemcinin maksimum hızında (örn: 180 MHz) çalışır. Güç ve hız gerektiren donanımlar (GPIO portları, DMA birimleri) doğrudan bu ana otoyola (AHB1, AHB2) bağlıdır.

- APB (Advanced Peripheral Bus): Ana otoyoldan ayrılan daha yavaş tali yollardır. Kendi içinde ikiye ayrılır:

- APB2: Yüksek hızlı tali yol (Max 90 MHz). (Örn: Hızlı Timer'lar, SPI1).

- APB1: Düşük hızlı tali yol (Max 45 MHz). (Örn: Standart Timer'lar, I2C, UART).

- Neden Önemli? Bir donanımı kullanırken hangi hıza sahip olduğunu bilmek, zamanlama (delay) hesapları ve baud-rate ayarları için hayatidir.

## C Struct'ları (Yapıları)
Önceki bölümlerde register adreslerine manuel olarak Hexadecimal sayılar (0x40020000 gibi) yazdık. Ancak binlerce register'ı ezberlemek imkansızdır.

- ST'nin Çözümü (stm32f4xx.h): ST firması, tüm hafıza haritasını (Memory Map) C dilindeki struct (yapı) objeleri haline getirmiştir.

#### Nasıl Kullanılır?
Pointer to Struct operatörü olan -> (ok işareti) kullanılır.

- Artık 0x40023800 adresine manuel veri yazmak yerine şunu yazarız:
- RCC->AHB1ENR |= 1;
- (Bu kodun meali: Cihaz hafızasındaki RCC bloğuna git, onun içindeki AHB1ENR register'ını bul ve ilgili bitini 1 yap.)

### Clock (Saat) Aktivasyonu
Eğer bir çevrebirimini (Örneğin Port A'yı veya Timer 2'yi) kullanmak istiyorsan, önce onun bağlı olduğu veriyoluna (Bus) enerji/saat (Clock) vermelisin.

- Enerji tasarrufu sağlamak için STM32'de cihaz ilk açıldığında işlemci çekirdeği hariç TÜM donanımların elektriği/saati kapalıdır (Disabled). Saati açılmamış bir register'a değer yazmaya çalışmak (örn: pini High yapmaya çalışmak) hiçbir işe yaramaz.

#### Clock Nasıl Açılır? (RCC - Reset and Clock Control)
Her şey RCC modülü üzerinden yönetilir. Örneğin GPIOA birimi AHB1 hattına bağlıdır.

- // ÖRNEK: Port A'nın saat sinyalini (Clock) aktif et.
- // RCC'nin AHB1 Enable Register'ındaki ilgili biti SET ederiz.
- RCC->AHB1ENR |= (1 << 0); // GPIOAEN biti 0. bittir (Reference Manual'dan bakılır)
