# 7z Arşiv Desteği (Listeleme ve Çıkarma)

## Amaç
Repo içinde 7z arşivlerini güvenle listelemek ve çıkarmak.
Araç eksikliği nedeniyle ilerlemeyi kilitlememek.

## Kapsam
- Listeleme: arşivde hangi dosyalar var?
- Çıkarma: hedef klasöre güvenli şekilde çıkartma
- Şifreli arşiv: parola gerekirse açık hata

Bu modül “sihir” yapmaz.
Parola yoksa şifreyi kırmaya çalışmaz.
Şifreleme varsa adını koyar ve durur.

## Tasarım
Tek giriş noktası vardır:
- `list_sevenz(...)`
- `extract_sevenz(...)`

Backend seçimi:
- Varsayılan: `auto`
- Zorlamak için:
  - `DEVOS_SEVENZ_BACKEND=py7zr`
  - `DEVOS_SEVENZ_BACKEND=7z-cli`

## Backend Seçenekleri

### 1) py7zr (Önerilen)
Avantaj:
- Python içinde çalışır.
- CI’da kolay test edilir.
- Bir komutla bağımlılık yönetilir.

Kurulum:
- pip: `pip install py7zr`
- uv: `uv pip install py7zr`

Not:
- Bazı codec/şifreleme senaryoları ek bağımlılık ister.
- Bunları pip çoğu zaman otomatik getirir.

### 2) 7z-cli (Saha Aracı)
Avantaj:
- Yerel sistem aracıdır.
- Büyük arşivlerde hızlıdır.

Kurulum (Windows):
- `winget install -e --id 7zip.7zip`

Kurulum (Linux örnek):
- Debian/Ubuntu: `sudo apt-get install -y p7zip-full`

Dikkat:
- Parolayı komut satırında vermek süreç listesinde görünebilir.
- Bu yüzden CLI backend ile parola kullanımı “son çare” olmalı.

## Hata Modeli
- `ToolMissingError`: hiçbir backend yok
- `PasswordRequiredError`: arşiv parola istiyor (muhtemelen encrypted header/AES)
- `UnsupportedArchiveError`: bozuk arşiv veya plain 7z değil (ör. SFX)
- `SevenZError`: diğer hatalar

## Kullanım Örnekleri

### Listeleme
```python
from devos.sevenz import list_sevenz

r = list_sevenz("sample.7z")
print(r.backend)
print(len(r.files))
print(r.files[:10])
```

### Çıkarma
```python
from devos.sevenz import extract_sevenz

r = extract_sevenz("sample.7z", "out/")
print(r.backend, r.extracted_count)
```

### Backend Zorlama
```bash
export DEVOS_SEVENZ_BACKEND=py7zr
pytest -q
```

## Smoke Test
Aşağıdaki kontrol, sistemin ayakta olup olmadığını söyler:

- py7zr backend:
  - `python -c "import py7zr; print(py7zr.__version__)"`

- 7z-cli backend:
  - `7z -h` (Windows)
  - `7z` yoksa: `winget install -e --id 7zip.7zip`

## Sorumluluk
Bu modül bir kapı görevlisi gibi davranır.
Ne zaman içeri alacağını bilir.
Ne zaman duracağını bilir.
Yıkmadan sınır koyar.
Kullanıcıya güven üretir.
