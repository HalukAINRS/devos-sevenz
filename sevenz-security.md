# 7z Güvenlik Notu

## Amaç
Arşiv açma işlemi sırasında kullanıcıyı ve sistemi korumak.
Bunu tahakkümle değil sınır koyarak yapmak.
Güven üretmek.

## Tehdit Modeli
Arşiv, dış kaynaktan gelir.
Arşiv içeriği kötü niyetli olabilir.

Tipik riskler:
- Path traversal: `../` ile hedef dizinden kaçma
- Absolute path: `/etc/...` veya `C:\...`
- UNC: `\\server\share`
- Çok büyük dosya / zip bomb benzeri etki
- Şifreli arşiv: parola olmadan “yanlış denemeler” ve log sızıntısı

## Uygulanan Koruma
Extraction öncesi arşiv içi member path’leri kontrol ederiz.

Reddedilenler:
- Absolute path
- Drive letter / UNC
- `..` segmenti

Bu kontrol “sertlik” değildir.
Bu, sorumluluktur.
Bunu gösteriye çevirmeyiz.
Sessizce uygularız.

## Parola Politikası
- Parola yoksa şifreyi kırmaya çalışmayız.
- Parola gerekiyorsa `PasswordRequiredError` döndürürüz.
- CLI backend ile parola geçmek risk taşır.
  - Süreç listesi ve log’lar parolayı sızdırabilir.
  - Bu yüzden CLI parolası “son çare” olmalı.
- Mümkünse py7zr ile in-process parola kullan.

## Kaynak Kullanımı
Büyük arşivlerde:
- Disk alanı
- RAM
- Süre
kontrol edilmelidir.

Bu repo seviyesinde “limit” şart değil.
Ama limit ihtiyacı doğarsa açıkça tanımlarız:
- maksimum toplam çıkarma boyutu
- maksimum dosya sayısı
- maksimum tekil dosya boyutu

## Sorumluluk
Bu modülü kullanan kişi şunu bilir:
- Arşiv bir veri değil, bir sınırdır.
- Sınırı korumak, kullanıcıya saygıdır.
