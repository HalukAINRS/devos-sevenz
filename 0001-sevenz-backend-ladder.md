# ADR-0001: 7z Arşiv Desteği için Backend Merdiveni

## Amaç
7z arşivlerini listeleme/çıkarma sürecini araç eksikliğinden bağımsız hale getiriyoruz.
Hedefimiz tekrarlanabilirlik.
Hedefimiz güvenlik.
Hedefimiz net hata.

## Zemin
Bazı ortamlarda 7z/7za/7zr komutu yok.
Bazı ortamlarda py7zr yok.
Bazı ortamlarda libarchive/bsdtar yok.
Bu durumda “format düzeyinde analiz” dışında ilerleme tıkanıyor.

Biz bu tıkanmayı repo seviyesinde çözeriz.
Araç yoksa bile aynı arayüzü sunarız.
Aracı olan ortamda hızdan vazgeçmeyiz.

## Karar
Tek bir 7z arayüzü tanımlarız:

- `list_sevenz(path, password=None) -> ListResult`
- `extract_sevenz(path, dest, password=None) -> ExtractResult`

Arka planda deterministik bir backend merdiveni kullanırız:

1. `py7zr` (in-process)
2. `7z-cli` (7z/7za/7zr/7zz)
3. Yoksa: `ToolMissingError` (açık ve aksiyon odaklı)

Ayrıca deterministik test ve debug için bir çevre değişkeni tanımlarız:

- `DEVOS_SEVENZ_BACKEND=py7zr | 7z-cli`

## Gerekçeler
- py7zr “kütüphane” olarak çalışır. CI’da taşınır.
- 7z-cli “saha aracı”dır. Windows ve Linux’ta yaygındır.
- Tek arayüz, tüketici kodu sade tutar.
- Tek hata modeli, log’ları anlaşılır kılar.

## Güvenlik İlkesi
Arşiv açmak bir “koruma sorumluluğu” doğurur.
Biz bunu kontrol kurmak için değil güven üretmek için üstleniriz.
Bu yüzden extraction öncesi path güvenliği kontrolü yaparız.

Kurallar:
- Absolute path reddedilir.
- Drive letter / UNC reddedilir.
- `..` segmenti reddedilir.

Bu yaklaşım şiddetsiz adalet gibidir.
Sınırı net çizer.
Kimseyi ezmez.
Sistemi korur.

## Sonuçlar
- Artık “araç yok” durumu bir belirsizlik değil.
- Net bir hata ve net bir kurulum yolu var.
- CI iki backend’i de doğrular.
- Extraction yolu güvenli sınırlar içinde kalır.

## Sorumluluk
- Bu modülü değiştiren kişi testleri de günceller.
- Bu modülün dokümantasyonu güncel tutulur.
- Üçüncü parti lisans notları repo’da yaşar.
