# Arda Moon Valley — Planetary Analog Discovery Model (PADM)

**Moon Valley**, Dünya'daki 50 Mars/Ay benzeri alanı referans alan ve NASA POWER açık çevresel verileri üzerinden aday bölge araştırmayı amaçlayan eğitim ve araştırma prototipidir.

## Kullanım

Siteyi açmak için yayımlanmış `index.html` adresini ziyaret edin. Harita ve 50 referans konum, sayfaya gömülü verilerden gelir. NASA POWER veri çağrıları tarayıcı üzerinden yapılır; ağ bağlantısı ve NASA servis erişimi gerekir. Veri elde edilmemişse hesaplanmış NASA ölçümü varmış gibi sunulmaz.

## Dosyalar

- `index.html` — kendi CSS ve JavaScript kodunu içeren ana uygulama.
- `data/referans_50_bolge.csv` — Arda'nın 50 bölgelik atlasından aktarılan referans konumlar.
- `docs/BILIMSEL_YONTEM.md` — eşleştirme yaklaşımı, sınırlılıklar ve doğrulama notları.
- `.github/workflows/pages.yml` — GitHub Pages üzerinden otomatik dağıtım iş akışı.

## Bilimsel not

Çevresel referans yakınlığı, Mars'a veya Ay'a fiziksel benzerliğin doğrulanmış yüzdesi değildir. Adayların jeolojik ve topoğrafik bağımsız verilerle doğrulanması gerekir. Uygulama NASA ya da ESA'nın resmî ürünü değildir.

## Yayınlama

GitHub deposunun **Settings → Pages** bölümünde **Build and deployment → Source: GitHub Actions** seçin. `main` dalına gönderilen her yeni değişiklikle `Deploy GitHub Pages` iş akışı dağıtım denemesi başlatır. Proje deposu herkese açık olmalıdır (ücretsiz GitHub Pages için).
