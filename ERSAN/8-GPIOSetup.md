# GPIO'nun Aktif Edilmesi

## Standart 4 Aşama
Hiçbir HAL veya hazır kütüphane kullanmadan bir pini hayata geçirmek için sırayla uygulanması gereken 4 adım vardır.

- İlgili portun enerji/saat (Clock) hattını aç (RCC).

- Pinin çalışma modunu "Output" (Çıkış) olarak ayarla (MODER).

- Pinin çıkış elektriksel tipini "Push-Pull" yap (OTYPER).

- Pine High (1) veya Low (0) sinyali gönder (ODR).

## 🕵️‍♂️ Döküman Okuma Metodolojisi 
Herhangi bir çevre biriminin aktif edildiği bus yolları ve registerları hangi donanımın kullanıldığına göre değişir.

### Aşama A: Datasheet

- Amaç: Kullanacağımız donanımın hangi "Bus" (Veriyolu) üzerinde olduğunu bulmak.

- Eylem: İşlemcinin (STM32F411) Datasheet'i açılır. İçindekilerden "Block Diagram" (Blok Diyagramı) sayfasına gidilir.

- Bulgu: Diyagramda GPIOA, GPIOB vb. portların doğrudan AHB1 veriyoluna bağlı olduğu görülür. Artık elektriği nereden vereceğimizi biliyoruz.

### Aşama B: Reference Manual (RCC - Saat Aktivasyonu)

- Amaç: AHB1 veriyolundaki GPIOA şalterini açmak.

- Eylem: Reference Manual açılır. Reset and Clock Control (RCC) bölümüne gidilir. Ardından "RCC Registers" alt başlığına geçilir.

- Bulgu: Madem GPIO AHB1'e bağlı, o zaman RCC_AHB1ENR (AHB1 Peripheral Clock Enable Register) bulunmalıdır. Register haritasına bakıldığında GPIOAEN (GPIO A Enable) bitinin 0. bit olduğu görülür.

### Aşama C: Reference Manual (GPIO - Pin Konfigürasyonu)

- Amaç: Pin modlarını ayarlayan register'ların adreslerini ve bit görevlerini öğrenmek.

- Eylem: Reference Manual'da General-purpose I/Os (GPIO) bölümüne ve oradan "GPIO Registers" başlığına gidilir.

- Bulgu: * GPIOx_MODER (Mode Register) incelenir. Her pin için 2 bit ayrıldığı ve "Output" modunun 01 olduğu görülür.

- GPIOx_OTYPER (Output Type Register) incelenir. Push-Pull için 0 yazılması gerektiği anlaşılır.

- GPIOx_ODR (Output Data Register) incelenir. İlgili pine voltaj vermek için bitin 1 yapılması gerektiği onaylanır.

## 🛠 Adım Adım Kod İnşası (Pin PA0 Örneği)

### Adım 1: Clock (Saat) Hattını Açmak

- Döküman takibiyle (Aşama B) öğrendiğimiz üzere, GPIOA, AHB1 hattındadır ve 0. bittir.

- // AHB1 Enable Register'ın 0. biti GPIOA'yı aktifleştirir.
- RCC->AHB1ENR |= (1 << 0); 


### Adım 2: Pini Output (Çıkış) Olarak Ayarlamak (MODER Register)

- Reference Manual'a göre (Aşama C) GPIOx_MODER register'ı her bir pin için 2 bit kullanır (00: Input, 01: Output, 10: Alternate Function, 11: Analog).
- PA0 pini için hedef bitlerimiz 0. ve 1. bitlerdir. Biz bu iki biti 01 yapmak istiyoruz.

- ÖLÜMCÜL HATA: Eğer eski değeri silmeden GPIOA->MODER |= (1 << 0); yaparsak ve o pin önceden Analog (11) modundaysa, sonuç yine bozuk olacaktır.

- DOĞRU YÖNTEM (Clear & Set): Önce iki biti de sıfırla, sonra 1 yaz. Pin numarası 0 olduğu için kaydırma miktarı (Pin * 2) = 0'dır.

- GPIOA->MODER &= ~(3 << (0 * 2)); // Adım 2.1: İlgili 2 biti kazı ve temizle (3 = 0b11)
- GPIOA->MODER |=  (1 << (0 * 2)); // Adım 2.2: Sadece ilgili bölgeye "01" (Output) yaz


### Adım 3: Çıkış Tipini Ayarlamak (OTYPER Register)

- Pinin elektriği nasıl süreceğine karar veririz: Push-Pull veya Open Drain. LED yakmak için Push-Pull gereklidir ve bu durum bitin 0 olmasıyla sağlanır. Bu register her pin için 1 bit kullanır.

- GPIOA->OTYPER &= ~(1 << 0); // PA0'ı Push-Pull (0) moduna zorla


### Adım 4: Veriyi Göndermek (ODR - Output Data Register)

- Artık pini kullanabiliriz. ODR (Output Data Register) her pin için 1 bit barındırır.

- // Pini HIGH (Yanar) yapmak için:
- GPIOA->ODR |= (1 << 0); 

- // Pini LOW (Söner) yapmak için:
- GPIOA->ODR &= ~(1 << 0); 

