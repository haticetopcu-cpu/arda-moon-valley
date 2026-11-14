# Moon Valley — Bilimsel model ve jüri için doğrulama notları

**Sürüm:** PADM 0.2.1 | **Tarih:** 14 Kasım 2026 | **Proje:** Arda'nın Dünya Analog Atlası

## Araştırma sorusu

NASA Space Apps 2026 görevinde Dünya, Ay ve Mars'ın açık verilerinden yararlanılarak yeni Dünya analog adayları belirlenmesi ve tanımlanması bekleniyor. Moon Valley'in araştırma hipotezi: Bilinen Dünya analoglarının belirli, ölçülebilir çevresel/topografik özellikleri kullanılarak benzer özellikler taşıyan yeni *aday koordinatlar* önceliklendirilebilir. Aday koordinatın gerçekte Ay/Mars araştırması için uygun olması ayrıca jeolojik veri ve uzman doğrulaması gerektirir.

## Kullanılan ilk veriler

- Arda'nın özgün atlasındaki 50 saha: ad, ülke, kıta, Ay/Mars etiketi, yüzey sınıfı, bilimsel açıklama, yaklaşık merkez koordinatı. Bunlar **literatürden derlenmiş referans kayıtlardır**; eşit kalitede sayısal NASA ölçümleri değildir.
- NASA POWER Climatology API: her koordinat için yıllık 2 m sıcaklık (T2M, °C), düzeltilmiş günlük yağış (PRECTOTCORR, mm/gün), 2 m bağıl nem (RH2M, %) ve yüzeye ulaşan kısa dalga güneş ışınımı (ALLSKY_SFC_SW_DWN, kWh/m²/gün). Veri API'den çekildiğinde kaynak URL'si, orijinal JSON yanıtı ve erişim zamanı kaydedilir.
- NASA LOLA, NASADEM ve EMIT katalogları farklı görevler için uygun ek kaynaklar olarak belirlenmiştir. **Bu sürümde bu veri setlerinin sayısal raster analizi yapılmış değildir.**

## Modelin matematiği

Seçilen hedef t için, aday Dünya noktası x ve o hedefteki bir referans Dünya analogu r arasında:

`d_t(x,r) = sqrt( sum_j w_{t,j} * ( (x_j - r_j) / tau_j )^2 )`

`I_t(x) = 100 * exp( - min_{r in R_t} d_t(x,r) )`

- `R_t`: Arda atlasından hedefe uygun **önceden tanımlanmış** referans alt kümesi. 50 sahanın tamamının tek bir bilimsel hedefe uygun olduğu varsayılmaz.
- `x_j, r_j`: NASA POWER tarafından sağlanan Dünya klimatolojisi ölçümleri.
- `w_{t,j}`: araştırma hedefine göre toplamı 1 olan **deneysel** önem ağırlıkları. Örnek Mars kurak saha görevi için yağış 0.45, nem 0.30, sıcaklık 0.15, ışınım 0.10.
- `tau_j`: ölçüm birimlerini ortak ölçekte ele almak için önceden tanımlanan deneme ölçekleri; sıcaklık 15 °C, yağış 1.6 mm/gün, nem 25 yüzde puan, ışınım 2 kWh/m²/gün.
- `I_t`: Dünya referanslarına **çevresel yakınlık endeksi**, 0–100. Bu bir Ay/Mars fiziksel benzerlik yüzdesi, bilimsel başarı olasılığı, onaylı NASA puanı veya yeni bilimsel keşif ispatı değildir.

Aday ve en az iki Dünya referansı için dört ölçümün de geçerli olması gerekir. Eksik veri bulunduğunda **puan üretilmez**. Jezero karbonatı ve Ay bazaltı gibi jeolojik hedefler için NASA POWER'a dayanarak sayısal toplam puan *üretilmez*.

## Yeni aday taraması

1. Arda'nın atlasından araştırma hedefiyle ilgili referanslar seçilir.
2. NASA POWER API gerçek ölçümleri alınır. Tarama alanında tanımlı 3×3 örnek koordinat ağı değerlendirilir.
3. Orijinal 50 saha merkezine 120 km'den yakın noktalar otomatik öneri taramasında elenir. Bu uzaklık jeolojik açıdan gerçek saha sınırı değildir; **bir tekrar-önleme eşiğidir**.
4. Dört ölçümü olan adaylar hesaplanan çevresel yakınlık endeksine göre sıralanır.
5. Aday, jeolojik kanıtlar, topografya, Ay/Mars hedef alanı ve saha erişilebilirliği değerlendirilmeden **doğrulanmış analog olarak adlandırılmaz**.

## Ay güney kutbu hedefi

NASA LRO/LOLA'nın Shackleton çevresi dâhil güney kutbu için yüksek çözünürlüklü topografya, eğim ve hata haritaları resmî kaynaklarıdır. Dünya'nın kutup bölgeleri görev operasyonları için ön araştırma olabilir. Ancak Dünya klimatolojisi; Ay'ın vakumu, düşük yerçekimi, kalıcı gölge, yüzey sıcaklıklarının uç değerleri veya aydınlanma geometrisini taklit etmez. Bu nedenle bu prototipte **Ay güney kutbuyla fiziksel eşleşme** iddiası kurulmaz.

## Prototip doğrulaması

- Tarayıcı işlev testleri: 50 atlas kartı, harita pinleri, hedef değiştirme, aday ekleme, JSON alma/işleme, rapor indirme; JavaScript çalışma hatası gözlenmedi.
- Otomatik fonksiyon testi **sahte NASA API yanıtlarıyla** yapıldı; bu, arayüzün ve matematiksel hesaplamanın kod düzeyindeki çalışmasını kanıtlar, canlı NASA verilerinin dış ağ üzerinden başarıyla indirildiğini kanıtlamaz.
- Dış ağ erişimi bu geliştirme ortamında mümkün olmadığından NASA POWER'ın gerçek yanıtlarından doğrulanmış sayısal sonuçlar dosyaya gömülmedi. Tarayıcınızda **NASA POWER ile tara** eylemini çalıştırıp sonuçları ham JSON dosyasıyla birlikte saklayın.
- `Moon_Valley_Terrain_Analyzer.py` ayrı bir ileri analiz betiğidir: gerçekte indirilen NASADEM ve LOLA yükseklik rasterları sağlanırsa karşılaştırılabilir çözünürlükte eğim dağılımlarını özetler. Bu betik mevcut HTML'deki endekse otomatik entegre **değildir**.

## Jüriye gösterilecek kanıt zinciri

1. 50 alanın mevcut atlasındaki bilgi ve jeolojik gerekçeler.
2. Hedef: Mars kurak saha operasyonu veya Ay güney kutbu kutup görevleri.
3. Seçilen yeni koordinat ve orijinal 50 referanstan en yakınlarına mesafesi.
4. NASA POWER ölçüm tablosu (API yanıtı, URL, erişim tarihi).
5. Kullanılan matematiksel formül, referans saha ve endekse katkı yapan ölçümler.
6. Değerlendirilmeyen jeolojik/topografik özellikler ve sonraki doğrulama adımları.

**Yapılmaması gereken iddialar:** “Yeni Dünya alanı bilimsel olarak ilk kez keşfedildi”, “Ay/Mars'a %98 benzer”, “NASA tarafından doğrulandı”, “LOLA/EMIT hesaplaması tamamlandı”. Bunların hiçbiri mevcut prototiple kanıtlanmış değildir.

## Resmî kaynaklar

- 2026 NASA Space Apps görev metni: https://www.spaceappschallenge.org/2026/challenges/permanent-moon-base-locations-and-mars/
- NASA POWER: https://power.larc.nasa.gov/docs/services/api/temporal/climatology/
- NASA NASADEM: https://lpdaac.usgs.gov/products/nasadem_hgtv001/
- NASA EMIT L2B: https://doi.org/10.5067/EMIT/EMITL2BMIN.001
- NASA LRO/LOLA polar elevation and slope maps: https://pgda.gsfc.nasa.gov/products/78
- NASA Mars Trek: https://trek.nasa.gov/mars/
