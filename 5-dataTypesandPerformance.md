#  C Veri Tipleri, Bellek Yönetimi ve Performans Optimizasyonu

## Değişken Seçimi Neden Önemlidir?
- Yüksek seviyeli dillerde (Python vb.) değişken tipleri derleyiciye bırakılır. Ancak STM32 gibi kısıtlı RAM'e (SRAM) sahip ortamlarda değişken tipi; bellek tüketimi ve işlem hızı (execution time) demektir. 1000 elemanlı bir diziyi (array) gereksiz yere int olarak tanımlamak, 8KB yerine 32KB RAM harcamak demektir ve gömülü sistemlerde bu lüksümüz yoktur.

# STM32 (32-Bit ARM) Mimarisinde Veri Boyutları
Keil üzerinde sizeof() operatörü ile yapılan canlı bellek testlerinin sonuçları:

- char: 8-bit (1 byte)
- short: 16-bit (2 byte)
- int: 32-bit (4 byte)
- float: 32-bit (4 byte)
- long: 32-bit (4 byte)
- long long: 64-bit (8 byte)
- double: 64-bit (8 byte)

### <stdint.h> Standardizasyonu **uint32_t** Nedir?
Bir int değişkeninin boyutu derleyiciden derleyiciye değişebilir (Bazılarında 16-bit, bazılarında 32-bit). Bare-metal programlamada donanım (register'lar) kesin sınırlarla çizildiği için bu belirsizlik kabul edilemez.

- Çözüm: Standart int tanımları (int8_t, uint16_t, uint32_t) kullanmak.
- Açılımı: u (unsigned/işaretsiz, sadece pozitif), int (tamsayı), 32 (bit genişliği), _t (type/tip).

## Veri Tipi ve Performans Arasındaki İlişki
Performans Ölçümleri:

- STM32, 32-bitlik bir işlemcidir. Bu işlemci bir matematik işlemi yaparken:
- char (8-bit) ile: 45 mikro-saniye
- short (16-bit) ile: 51 mikro-saniye
- int (32-bit) ile: ~39.7 mikro-saniye sürüyor!

- Sonuç: İşlemci veri yolları (bus) 32-bit olduğu için, 32-bitlik bir veri (uint32_t), 8-bitlik bir veriden (uint8_t) daha hızlı işlenir! Çünkü 8-bitlik veriyi işlerken işlemci ekstra "hizalama" (alignment) eforu harcar.
- Altın Kural: Register adreslemelerinde ve hız gerektiren döngülerde bellek sıkıntısı yoksa daima uint32_t kullanılmalıdır.

### 💥 Kayan Nokta (Floating Point) 
STM32 serisinde (FPU - Floating Point Unit olsa bile) float ve double kullanımı korkunç performans kayıplarına yol açar.

- 32-bit int işlemi: ~40 µs
- 32-bit float işlemi: ~109 µs (Neredeyse 2.5 kat daha yavaş!)
- 64-bit double işlemi: ~873 µs (NASA'da çalışmıyorsan kullanma!)

#### Mühendislik Çözümü (Integer Scaling)
Eğer 1.253 Volt ölçtüyseniz, bunu asla float v = 1.253; olarak tutmayın.
Doğru Yaklaşım: Değeri milivolta çevirin. uint32_t mv = 1253;. Bu sayede kayan nokta matematiğinden kurtulur ve inanılmaz bir performans (1 milisaniye vs 377 mikrosaniye) kazanırsınız.
