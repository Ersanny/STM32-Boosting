# İkili Kod (Binary) Operasyonları ve Bit Maskeleme (Bit Masking)
- Her bir Bit (0 veya 1), donanımsal bir şalterdir (Örn: Bir pini açan, bir timer'ı başlatan veya bir kesmeyi tetikleyen anahtar). İşlemcinin hemen yanındaki ultra-hızlı hafıza alanları olan Register'lara bit seviyesinde erişmek, CPU'nun maksimum hızda çalışmasını sağlar.

## Doğrudan Atama (=) Operatörü Neden Yanlıştır?
- Eğer bir register'daki (örn: Port A'nın Output Data Register'ı) sadece 1. pini aktif etmek için GPIOA_ODR = 0b00000010; yazarsanız, o pini High (1) yaparsınız ANCAK geriye kalan tüm pinleri (varsa daha önce açılmış olanları) Low (0) yaparak bozarsınız.

- Kural: = operatörü, register'ın tüm hafızasını siler ve üzerine yazar. Sadece tüm portu sıfırlamak veya tam bir byte dizisi yazmak istiyorsak kullanılır. Tekil pin kontrolünde asla kullanılmamalıdır.

## Bit Manipülasyonu İşlemleri
- Register'lardaki diğer bitlerin durumunu (state) koruyarak sadece hedef biti değiştirmek için aşağıdaki operatörler kullanılır:

### OR (|) Operatörü -> SET (Biti 1 Yapmak İçin)
- Mantık: Sadece ekleme yapar (1 yapar). Sıfır (0) olan yerleri değiştirmez.

- Kullanım: MyByte |= 0b10000001; (Sadece en baştaki ve en sondaki biti 1 yapar, aradakilere dokunmaz).

### AND (&) Operatörü -> CLEAR (Biti 0 Yapmak İçin)
- Mantık: Sadece silme (0 yapma) işlemi için kullanılır. 1 olan yerler, hedef register'daki eski veriyi aynen korur.

- Kullanım: MyByte &= 0b11111110; (Sadece en sağdaki 0. biti sıfırlar, diğer 1'ler eski durumu korur).

### NOT/Complement (~) Operatörü -> REVERSE (Tersine Çevirmek)

- Mantık: Tüm bitleri tam tersine çevirir (1'leri 0, 0'ları 1 yapar).

### SHIFT (<< ve >>) Operatörü -> Hedefi Kaydırma

- Mantık: Bir biti (genellikle 1 sayısını) istediğimiz pozisyona kaydırarak hedef alırız. Maske oluşturmanın temelidir.

- Örnek: 1 << 5 demek, 00000001 sayısını 5 kez sola kaydırarak 00100000 elde etmektir. (Yani 5. biti hedef aldık).

## BİT MASKELEME (Bit Masking)
- Yukarıdaki araçları birleştirerek, "Süper Önemli Pinimizi (Örn: Pin 5)" diğer pinlere asla zarar vermeden nasıl kontrol ederiz?

### Hedef Biti High (1) Yapmak (SET)

- // Sadece 5. biti 1 yap, diğer bitlerin eski durumunu koru (OR kullan)
- Register |= (1 << 5);

### Hedef Biti Low (0) Yapmak (CLEAR)
- Bu işlem 2 aşama gerektirir:

- (1 << 5) bize 00100000 verir.

- ~(1 << 5) bu maskeyi tersler ve 11011111 yapar. (Sadece hedef 0, diğerleri 1).

- Bu terslenmiş maskeyi & (AND) ile register'a uygularsak, 1 olan yerler eski durumu korur, 0 olan 5. bit ise sıfırlanır.

- // Sadece 5. biti 0 yap, diğer bitlerin eski durumunu koru (AND ve NOT kullan)
- Register &= ~(1 << 5);
