# Akor Kutusu

Gitar şeklinde, 9 mekanik tuşlu bir USB klavye kutusu ve ona eşlik eden Python masaüstü
karaoke uygulaması.

- **Kutu:** ESP32-S3 (native USB, TinyUSB HID klavye). 7 akor tuşu + 2 değiştirici tuş.
  Her tuş, gerçek klavyelerde olmayan bir F tuşu gönderir (F13–F21).
- **Uygulama:** Python + PySide6. Tuşları arka planda (global) dinler, şarkı sözlerini ve
  sıradaki akoru gösterir. Ritmi kullanıcı belirler; uygulama sadece yol gösterir.
- **Şarkı formatı:** ChordPro (`.cho`). Şarkıları kullanıcı yapıştırır/yükler.
  **Sitelerden otomatik veri çekme yok** (telif ve site kuralları).

Ayrıntılı yol haritası: `Akor_Kutusu_Yapim_Rehberi.pdf` (Faz 1–9).

## Klasör yapısı

| Klasör      | İçerik                                         |
|-------------|------------------------------------------------|
| `firmware/` | ESP32-S3 PlatformIO projesi (framework: arduino) |
| `app/`      | Python + PySide6 masaüstü uygulaması           |
| `songs/`    | Örnek `.cho` (ChordPro) şarkı dosyaları        |
| `case/`     | OpenSCAD kutu tasarımı                         |
| `docs/`     | Notlar                                         |

## Tuş eşleme tablosu (tüm projede sabit)

| Tuş | Gönderilen kod | HID kodu | ESP32-S3 pini | Görev          |
|-----|----------------|----------|---------------|----------------|
| C   | F13            | 0x68     | GPIO 4        | Akor           |
| Dm  | F14            | 0x69     | GPIO 5        | Akor           |
| Em  | F15            | 0x6A     | GPIO 6        | Akor           |
| F   | F16            | 0x6B     | GPIO 7        | Akor           |
| G   | F17            | 0x6C     | GPIO 15       | Akor           |
| Am  | F18            | 0x6D     | GPIO 16       | Akor           |
| E   | F19            | 0x6E     | GPIO 17       | Akor           |
| m   | F20            | 0x6F     | GPIO 18       | Majör ↔ minör  |
| 7   | F21            | 0x70     | GPIO 8        | Yedili ekle    |

- Kablolama: her switch bir bacağı GPIO'ya, diğeri ortak GND'ye. `INPUT_PULLUP`, basınca LOW.
  Matris/diyot yok.
- **Kullanılmayacak pinler:** GPIO 19/20 (native USB D-/D+), GPIO 0, 3, 45, 46 (strapping).
- Pin değişirse önce bu tabloyu güncelle; firmware ve uygulama bu tabloya uymalı.
- Yedek plan: işletim sistemi F20/F21'i görmezse değiştiriciler
  `Ctrl+Alt+Shift+F13` / `Ctrl+Alt+Shift+F14` kombinasyonuna taşınır.

## Akor ve değiştirici mantığı

- `m` veya `7` **basılı tutulurken** bir akor tuşuna basılırsa birleşik akor üretilir:
  `G + 7 → G7`, `Am + m → A`, `C + m → Cm`, `Dm + 7 → Dm7`.
- Bu yüzden firmware press/release olaylarını ayrı ayrı gönderir.
- Akor adları: bemoller diyeze çevrilir (`Bb → A#`). 12 nota: C C# D D# E F F# G G# A A# B.

## Kod stili

- **Python:** 3.11+ (makinede şu an 3.14 kurulu), her fonksiyonda type hint, testler `pytest` ile.
- İş mantığı (`app/core/`) arayüzden (`app/ui/`) ayrı tutulur ve birim testleriyle korunur.
- pynput dinleyicisi ayrı thread'de çalışır, arayüze Qt sinyaliyle bağlanır.
- **Firmware:** pin ↔ tuş eşlemesi dosyanın başında tek bir dizi; `DEBUG` bayrağıyla seri log.
- Kullanıcıya dönük metinler ve yorumlar Türkçe olabilir; kod tanımlayıcıları İngilizce.

## Çalışma kuralları

1. **Her fazı ayrı bir Claude Code oturumunda yap.** Bir faz bitip test edilmeden diğerine geçme.
2. **Her fazdan sonra test et ve commit at.** Python tarafında `pytest` geçmeden commit yok.
3. Büyük işlerde önce plan çıkar, onay bekle.
4. Donanımı kullanıcı test eder; Claude kartı göremez. Seri port çıktısı/davranış kullanıcıdan istenir.
5. Yeni bir karar alındığında (pin değişikliği vb.) bu dosyayı güncelle.

## İlerleme

- [x] Hazırlık: klasör iskeleti ve CLAUDE.md
- [ ] Faz 1 – Donanım prototipi ve firmware
- [ ] Faz 2 – Uygulama iskeleti
- [ ] Faz 3 – Şarkı yükleme ve ChordPro dönüştürücü
- [ ] Faz 4 – Transpoze motoru
- [ ] Faz 5 – Mod A: Akorla ilerleme
- [ ] Faz 6 – Mod B: Sesle takip
- [ ] Faz 7 – Akor sesi (isteğe bağlı)
- [ ] Faz 8 – Gitar şeklinde kutu
- [ ] Faz 9 – Birleştirme, test ve paketleme
