# YZM411 Bilgisayarlı Görü — Ödev 1: Deney Öncesi Tahminler

**Ders:** YZM411 Bilgisayarlı Görü  
**Ödev:** Ödev #1 — Renk, Aydınlatma ve Örnekleme  
**Dosya Adı:** predictions.md  

---

## 1. Teorik Çerçeve

Bu çalışmadaki tüm deney adımları için hipotezler, kamera sensörünün fiziksel nesneyi değil sahneden yansıyan ışığı ölçtüğü ilkesi temel alınarak oluşturulmuştur:

$$f(x, y) = i(x, y) \cdot r(x, y)$$

Burada $i(x, y)$ aydınlatma bileşeni (şiddet ve spektral dağılım), $r(x, y)$ ise yüzeyin yansıtırlık katsayısıdır. Sayısal görüntü tensörünün aydınlatma değişimleri, renk uzayı dönüşümleri, nokta operasyonları ve uzamsal örnekleme karşısındaki davranışına dair deney öncesi öngörüler aşağıda sunulmuştur.

---

## 2. Deney Öncesi Hipotezler Özeti

| Deney Adımı | İncelenen Değişken | Önsel Hipotez / Beklenen Sayısal Davranış | Teorik Gerekçe |
| :--- | :--- | :--- | :--- |
| **Adım 4: Sayısal Yapı** | Aydınlatma şiddeti ve spektrumu | Gölgede $R, G, B$ değerleri orantılı zayıflar; sıcak lambada $R$ korunurken $B$ belirgin düşer. | $i(x, y)$ şiddeti azaldığında genlik düşer; akkor lambanın kırmızıya kayan spektrumu $B$ kanalını zayıflatır. |
| **Adım 5: Renk Eşikleme** | RGB vs. HSV renk uzayları | Sabit RGB eşikleri gölgede çöker; HSV tabanlı eşikleme nesne maskesini korur. | Gölgede genlik $R_{min}, G_{min}$ altına iner. HSV'de aydınlatma $V$'ye ayrışırken $H$ (ton) kararlı kalır. |
| **Adım 6.1: Parlaklık** | Sabit ofset ($b = +40$) | Ortalama parlaklık artışı $+40$'ın altında kalır ($\Delta \mu < 40$). | Parlak piksel değerleri ($>215$) 255 üst sınırına çarparak doymaya (clipping) uğrar. |
| **Adım 6.2: Gama ($\gamma = 0.5$)** | Konkav güç dönüşümü | Koyu ve orta tonlar belirgin şekilde açılır, görüntü ortalaması yükselir. | $s = c \cdot r^{0.5}$ fonksiyonu düşük ve orta gri seviyeleri yukarı öteler. |
| **Adım 6.3: Gama ($\gamma = 2.0$)** | Konveks güç dönüşümü | Görüntü dramatik biçimde kararır, koyu doku detayları kaybolur. | $s = c \cdot r^{2.0}$ fonksiyonu orta tonları sıfıra doğru bastırır. |
| **Adım 7.1: Alt-Örnekleme** | En yakın komşu ile $1/4$ küçültme | Küçük karakterler ve ince çizgiler kopar, aliasing/moiré desenleri oluşur. | Nyquist frekansını aşan yüksek frekanslı kenarlar antialiasing filtresi olmadan örneklenir. |
| **Adım 7.2: Alan Ortalaması** | $4 \times 4$ kutu filtreleme | Sahte desenler engellenir ancak karakterler homojen gri bloklara dönüşerek silinir. | Yüksek frekanslı kenarlar alçak geçiren filtreyle yumuşatılır, çözünürlük kalıcı kaybolur. |
| **Adım 7.3: Büyütme** | $4\times$ enterpolasyon | Kaybolan yüksek frekanslı metin detayları matematiksel olarak geri kazanılamaz. | Enterpolasyon mevcut düşük frekanslı veriyi çoğaltır, kayıp sensör bilgisini üretemez. |

---

## 3. Deney Bazlı Önsel Tahminler (İkişer Cümle)

### Adım 4: Görüntünün Sayısal Yapısı ve Aydınlatma Değişimi
> Gölge ortamında aydınlatma şiddeti ($i$) düşeceği için seçilen sarı nesne, beyaz kâğıt ve masa yüzeyine ait $R, G, B$ kanal değerlerinde homojen bir sayısal azalma gerçekleşecektir. Sıcak akkor lamba altında ise ışığın renk sıcaklığı düşeceğinden (kırmızıya kayan spektrum), sarı nesne ve beyaz kâğıt üzerinde $R$ kanalı korunurken veya artarken, $B$ kanalının sayısal değeri dramatik biçimde zayıflayacaktır.

### Adım 5: Sabit Renk Eşiklerinin Karşılaştırılması (RGB vs. HSV)
> Normal ışıkta belirlenen sabit RGB eşikleri, gölge altında piksel değerleri eşik alt sınırının altına düşeceği için hedef nesneyi segmente edemeyecek ve maske alan oranı sıfıra yaklaşacaktır. HSV uzayında ise aydınlatma zayıflaması $V$ (değer) kanalında izole edilip $H$ (ton) açısı dar bir aralıkta kararlı kalacağından, HSV tabanlı maske hedef nesneyi başarıyla koruyacaktır.

### Adım 6: Parlaklık ve Kontrast Manipülasyonları

#### 6.1. Parlaklık Kaydırması ($b = +40$)
> Matematiksel olarak $I_{yeni} = I + 40$ işlemi ortalama parlaklığı doğrudan 40 birim artırmalıdır. Ancak sahnede yer alan beyaz kâğıt ve parlak yansıma alanları 215'in üzerinde olduğundan 255 tavan değerinde doyum (clipping) yaşayacak ve bu nedenle ölçülen genel ortalama artışı 40'ın altında kalacaktır.

#### 6.2. Gama Dönüşümü ($\gamma = 0.5$ ve $\gamma = 2.0$)
> $\gamma = 0.5$ konkav güç dönüşümü düşük ve orta gri seviyeleri yukarı öteleyerek karanlık bölgeleri açacak ve genel parlaklık ortalamasını belirgin biçimde artıracaktır. Buna karşılık $\gamma = 2.0$ konveks dönüşümü piksel değerlerini sıfıra doğru bastırarak görüntüyü karartacak ve histogramı sol tarafa yığacaktır.

### Adım 7: Uzamsal Örnekleme ve Bilgi Kaybı

#### 7.1. $1/4$ Küçültme (En Yakın Komşu vs. Alan Ortalaması)
> En yakın komşu yöntemiyle $1/4$ küçültmede Nyquist sınırını aşan ince harf çizgileri örnekleme dışı kalarak kopacak ve belirgin örtüşme (aliasing) bozulmaları oluşacaktır. $4 \times 4$ piksellik alan ortalaması yöntemi ise yüksek frekanslı kenarları yumuşatarak sahte desenleri önleyecek, ancak metin karakterlerini okunamaz homojen gri bloklara dönüştürecektir.

#### 7.2. Yeniden Büyütme ile Geri Kazanım
> Küçültülen görüntülerin en yakın komşu enterpolasyonu ile tekrar $4\times$ büyütülmesi, yalnızca mevcut pikselleri bloklar halinde çoğaltacak ve kesikli (pixelated) bir görünüm üretecektir. Örnekleme aşamasında kaybolan yüksek frekanslı metin ve kenar bilgisi matematiksel olarak mevcut olmadığından geri kazanılamayacaktır.
