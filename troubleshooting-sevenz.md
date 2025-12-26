# Sorun Giderme: 7z (Listeleme/Çıkarma)

## Amaç
“Çalışmıyor” cümlesini hızlıca parçalamak.
Sebebi adıyla koymak.
Çözümü net vermek.

## 1) Backend yok: ToolMissingError
Belirti:
- `ToolMissingError: No 7z backend found...`

Çözüm yolları:
- Python yolu (önerilen):
  - `pip install py7zr`
  - veya `uv pip install py7zr`
- Windows yolu:
  - `winget install -e --id 7zip.7zip`
- Linux yolu:
  - Debian/Ubuntu: `sudo apt-get install -y p7zip-full`

Kontrol:
- `python -c "import py7zr"`
- `7z -h`

## 2) Parola istiyor: PasswordRequiredError
Belirti:
- `PasswordRequiredError`

Çözüm:
- Doğru parolayı temin et.
- Parolayı log’lama.
- CLI backend’de parolayı süreç listesine düşürme riskini bil.

Kontrol:
- Parola ile tekrar dene.
- Sonuç yine aynıysa arşiv “encrypted header” olabilir.

## 3) UnsupportedArchiveError
Belirti:
- `UnsupportedArchiveError`

Anlam:
- Arşiv bozuk olabilir.
- Plain 7z değil olabilir.
- SFX (self-extracting) olabilir.

Çözüm:
- Arşivi harici 7-Zip GUI ile kontrol et.
- Mümkünse arşivin “plain .7z” çıktısını al.

## 4) Path güvenliği hatası
Belirti:
- `Unsafe member path` veya `Path traversal detected`

Anlam:
- Arşiv içeriği hedef dizinden çıkmayı deniyor.

Çözüm:
- Arşivi güvenilir kaynaktan yeniden al.
- İçeriği bir güvenli ortamda incele.
- Bu kontrolü kapatma.
Bu kontrol, güven üretir.

## 5) CI’da bir backend çalışmıyor
Kural:
- CI iki backend’i de test eder.
- Birinin kırılması “uyarı” değil “iş durdurma”dır.

Çözüm adımları:
- `DEVOS_SEVENZ_BACKEND` ile lokal reproduksiyon yap.
- py7zr sürümünü sabitle (gerekirse).
- 7z-cli job’ında `p7zip-full` kurulumunu doğrula.
