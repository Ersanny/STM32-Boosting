# Hexadecimal Sayı Sistemi ve Gelişmiş Çoklu-Bit Maskeleme

## Neden Hexadecimal Kullanıyoruz?
- Binary (İkili) ve Decimal (Onlu) varken neden Hexadecimal'e (Onaltılık) ihtiyaç duyarız?
- Amaç, onaltılık sayıları onlu tabana çevirip büyüklüklerini anlamak DEĞİLDİR.
- Amaç tamamen donanım mimarisini insan beyninin kolayca yönetebileceği bloklara bölmektir.

- Altın Kural: 1 Hexadecimal Karakter = 4 Binary Bit
- Neden 1: Yazım Kısalığı ve Hız. 64-bitlik bir register'ı binary olarak yazmak (64 tane 1 ve 0) üretim/kodlama süresini uzatır. Hexadecimal ile bu sadece 16 karaktere düşer.
- Neden 2: Hata Engelleme (Error Reduction). 0b1010101010101010 yazarken bir sıfırı eksik yazmak veya yerini şaşırmak garantidir. Aynı sayıyı 0xAAAA olarak yazmak hem okunabilirliği artırır hem de hatayı neredeyse imkansız hale getirir.

- Not: C dilinde Hexadecimal sayılar 0x öneki ile başlar.

### Sık Karşılaşılan Şablonlar (Ezbere bilinmesi hız kazandırır):
- 0xA = 1010
- 0xC = 1100
- 0xF = 1111 (4 bitin tamamı High/Açık)

## Hexadecimal Shifting (Kaydırma) Yanılgısı
Hexadecimal sayılarla çalışırken yapılan en büyük mantık hatasıdır.

- Diyelim ki elimizde 0xC (Binary: 1100) var.
- Yanlış Mantık: 0xC << 1 yaparsam, C harfi bir yana kayar (Yani 0xC0 olur) sanmak YANLIŞTIR. Shift operatörü hexadecimal karakterleri değil, alttaki bitleri kaydırır.
- Doğru Mantık: Tam bir Hexadecimal basamağı (4 bitlik bir bloğu) kaydırmak istiyorsanız, 4 bit kaydırmanız gerekir.
- Doğru Kullanım: 0xC << 4 = 0xC0 (C harfi bir basamak sola kaydı).

### Çoklu Bit Maskelemesinde (Multi-Bit Masking) Bit Çarpışması
Sadece "tek bir pini" değiştirmeyi gördük. Ancak STM32'de genellikle 4 veya daha fazla bitlik konfigürasyon blokları bulunur (Örn: Bir pini Alternate Function yapmak için 4 bitlik ayar gerekebilir).

- Senaryo: Register'ın 24. bitinden başlayan 4 bitlik bir bloğu 0xC (1100) yapmak istiyoruz.
- Zayıf ve Yanlış Yöntem (Sadece OR kullanmak): Register |= (0xC << 24);
- Neden Yanlış? Eğer register'ın o bölgesinde önceden 0b1111 (Hex: F) değeri varsa, 1111 | 1100 işleminin sonucu yine 1111 (F) çıkacaktır. İstediğimiz C değerini yazamadık, sistem yanlış çalışıyor.

### 2 Aşamalı Güvenli Çoklu-Bit Maskeleme
Bir register bölgesine "çoklu bit" (Hexadecimal blok) yazılacaksa, o bölge önce "Sıfırlanmalı (Temizlenmeli)", ardından "Yazılmalıdır".

- Senaryo: 24. bitten itibaren 4 biti (yani 0xF uzunluğundaki bir alanı), 0xC değeri ile güncellemek.

#### Adım 1: Alanı Temizle (CLEAR)
Önce o bölgedeki eski çöpleri temizleyip 0000 yapmalıyız. Kalan bitlere dokunmamak için & (AND) ve ~ (NOT) kullanırız. Hedef bölge 4 bitlik olduğu için temizlik maskemiz 0xF (1111) olmalıdır.

-// 24. bitten başlayan 4 biti (0xF kadar alanı) sıfırla. Diğerlerine dokunma.
-Register &= ~(0xF << 24); 


#### Adım 2: Yeni Değeri Yaz (SET)
Alan temizlendiğine (0000 olduğuna) göre, artık güvenle yeni değerimizi | (OR) operatörü ile yerleştirebiliriz.

- // Temizlenmiş alana istediğimiz 0xC (1100) değerini yaz.
- Register |= (0xC << 24);


#### Kullanım:
- Register &= ~(0xF << 24); // Adım 1: Eski değeri kazı
- Register |= (0xC << 24);  // Adım 2: Yeni değeri yaz
