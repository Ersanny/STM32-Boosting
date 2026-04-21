# İlk Proje, ST-Link Konfigürasyonu ve Debugger Kullanımı

## Proje Hiyerarşisi ve Dosya Yönetimi
- Header Klasörü: Projeye bir de Headers isimli mantıksal klasör (group) eklenmelidir. Bu, .c (kaynak) ve .h (başlık) dosyalarını birbirinden ayırarak kod okunabilirliğini artırır.
 
- İnternetten veya başka bir projeden alınan kütüphane dosyalarını (Örn: gpio.c, gpio.h) sadece projenin bulunduğu Windows klasörüne kopyalamak yetmez. Bu dosyalar Keil içerisinden sağ tıklanıp *Add Existing Files to Group* seçeneği ile projeye açıkça tanıtılmalıdır.

## Kodlamaya Başlarken
- Donanımı test etmek için önceden hazırlanmış 3 kütüphane (clock, time, gpio) projeye dahil edilmiştir:

- Include İşlemi: #include "gpio.h" ve #include "time.h" ile ana programa dış modüller tanıtılır.

- Pin Konfigürasyonu: Döngüye girmeden önce ilgili pinler (Örn: PA8 ve PC9) Output (Çıkış) olarak ayarlanır. (Setup aşaması)

- Sonsuz Döngü *(while(1))*:
*pin_toggle()* fonksiyonu ile pinin durumu terslenir (High ise Low, Low ise High).
*delay_ms(500)* ile işlemci yarım saniye (500 ms) bekletilir.

## STM32'ye Kod Yükleme ve ST-Link Ayarları
Yazılan C kodunun derlenip (Build) makine koduna çevrilmesinden sonra karta yüklenmesi (Load) gerekir.

- Hata Durumu ("No Target Connected"): Varsayılan ayarlarla kod karta yüklenemez. Keil'e hangi programlayıcıyı kullandığımızı söylememiz gerekir.
  
- Donanım Ayarı: Options for Target (Sihirli Değnek ikonu) -> Debug sekmesi -> Sağ üstteki açılır menüden ST-Link Debugger seçilmelidir. (Eğer klon/farklı bir kart kullanılıyorsa CMSIS-DAP vb. seçilebilir).

- ST-Link seçildikten sonra Settings -> Flash Download sekmesine gidilir. "Reset and Run" kutucuğu işaretlenir.
Neden? Bu işaretlenmezse, kodu karta yükledikten sonra kod çalışmaya başlamaz; kullanıcının kart üzerindeki fiziksel 'Reset' butonuna basması gerekir. Bu ayar, yükleme biter bitmez kodun otomatik başlamasını sağlar.

## Hata Ayıklama (Debug Mode) 

- Zamanı Durdurmak: Debug moduna girildiğinde mikrodenetleyici çalışmayı durdurur ve ilk komutta bekler.

- Step-by-Step (Adım Adım Çalıştırma): Kodu satır satır çalıştırarak değişkenlerin ve pinlerin durumunu canlı olarak izleyebiliriz. Mantık hataları (Logic Bugs) bu şekilde bulunur (Videodaki pin isimlendirme hatasının bulunması gibi).

- Register (Bellek) Gözlemi: Çevre birimleri (Peripherals) menüsünden örneğin GPIOA seçildiğinde, işlemcinin iç donanım yapısı açılır.

- Kodda bir pini High yaptığımızda, gerçek donanımda ODR (Output Data Register) içindeki ilgili bitin 1 (işaretli) olduğunu canlı olarak görürüz. Bu, "Bare-Metal" felsefesinin kanıtıdır.

- Disassembly (Assembly Kodu Görünümü): Yazılan C kodunun işlemci tarafından makine koduna (Assembly) nasıl çevrildiğini anlık olarak gösterir. Optimizasyon analizleri için kritiktir.
