# Katkı Rehberi

## Amaç
Bu repo hızlı değil, sağlam büyüsün.
Her değişiklik iz bıraksın.
Her iz kanıt olsun.

## Temel İlke
Bir değişiklik öneriyorsan üç şeyi birlikte getir:
1) Plan
2) Uygulama
3) Test

Sadece fikir yetmez.
Sadece kod yetmez.
Sadece test yetmez.

## 7z Modülü İçin Standartlar

### Arayüz Sözleşmesi
Aşağıdaki fonksiyonlar korunur:
- `list_sevenz(...)`
- `extract_sevenz(...)`

Hata sınıfları korunur:
- `ToolMissingError`
- `PasswordRequiredError`
- `UnsupportedArchiveError`

### Backend Determinizmi
Test ve debug için:
- `DEVOS_SEVENZ_BACKEND=py7zr`
- `DEVOS_SEVENZ_BACKEND=7z-cli`

Bu değişkeni desteklemeyen PR kabul edilmez.
Çünkü deterministik yeniden üretim gerekir.

### Güvenlik
Extraction öncesi path güvenliği kontrolü zorunludur.
Bu kontrol “konfor” değil sorumluluktur.

## Test Çalıştırma

### Py7zr backend
```bash
python -m pip install -U pytest py7zr
export DEVOS_SEVENZ_BACKEND=py7zr
pytest -q
```

### 7z-cli backend
Linux (örnek):
```bash
sudo apt-get update
sudo apt-get install -y p7zip-full
python -m pip install -U pytest
export DEVOS_SEVENZ_BACKEND=7z-cli
pytest -q
```

## CI Beklentisi
- PR’da CI yeşil olmalı.
- CI kırıldıysa biz dururuz.
Onarım bitmeden ilerlemeyiz.

## Dokümantasyon
Davranış değiştiyse doküman da değişir.
Kısaltma yap.
Net yaz.
Sorumluluğu dağıtma.
