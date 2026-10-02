# STATE — Anlık Durum
Son güncelleme: 2026-10-02 (Oturum 13)

## Aktif İş
| İş | Klasör | Durum |
|---|---|---|
| Kaynak bağımsızlığı + model geçişi | work/2026-10-02-… + model-hafiza.md | 🔄 FAZ 1 v3 ONAYLI (27B ana KALIYOR; hedef = paralel iş + verimlilik + context ekonomisi) |
| Aşama 1 testi | work/…/cerceve.md | ✅ BİTTİ: VRAM düşmedi, worker 27B ile koexist (runs.csv 2026-10-02-01) |
| L1 boşta boşaltma | Bionic (lms + JIT) | ✅ **TEST BİTTİ (2026-10-02)**: unload 23574→1500 MiB; 2×GPU worker 18.5/101.9 tok/s; JIT reload 317.7s; final 23082 (oturum ayakta); SIRA = Mod B eşiği kararı + (ops.) warm teyit |
| Full veri toplama haftası | metrics/ | 🔄 10-02→10-09: ~%50 full, hedef ≥3; hafta sonu rapor |
| Delegation Worker B | PROTOCOL + delegation/ | ✅ operasyonel; runs.csv 7 kayıt |
| Oto-devir | agent/ | ✅ KAPANDI (Oturum 12): mevcut devir yeterli, yeni mekanizma yok |
| Sistem Değerlendirmesi | metrics/ | ✅ Ö1+Ö2 uygulandı; Ö3 açık (son 10-02) |
| EmberTale T1–T6 | Project EmberTale/ | 🕐 ayrı sohbet |

## Sıradaki Adım (YENİ SOHBET — devir)
1. **L1-B raporu + karar**: sonuç raporu FunkGoth'a (2026-10-02) → **Mod B eşiği kararı** (reload ≈5.3 dk → break-even ≈4300 output tok/batch); isteğe bağlı W1 warm teyidi (onay).
2. **Veri haftası** (→10-09): policy'ye göre full (run_id çift; yeni iş tipi daima); hafta sonu rapor + mekanizma tartışması.
3. **Ö3**: sonraki Sistem Değerlendirmesinde ana model ~32 tok API örneği → baseline bandı.
4. **FAZ 2 raporu** (→10-09 sonrası): iş tipi haritası, routing, paralel kapasite, iş-başı öngörü (GPU worker verisi besleyecek).

## Beklenen Kararlar (FunkGoth)
- **L1: B ONAYLI + TEST BİTTİ** — sonuç: runs.csv 2026-10-02-02/03; SIRA = Mod B eşiği kararı + (ops.) warm teyit onayı.
- Mimari ONAYLI (Oturum 11): GPU=1 kiracı; **Mod A** = interaktif (27B resident + workers CPU), **Mod B** = paralel batch (boşalt → 2×GPU worker → reload + entegre).

## Detay
- **Bionic ≠ LM Studio (klasik)** — iki ayrı uygulama; bu makinede yalnız Bionic kurulu, 27B'yi Bionic yükler (port 1234 = Bionic.exe). Klasik LM Studio ayarları (JIT/TTL/Auto-Evict, #2051) Bionic'e UYGULANMAZ. Boşaltma yolları (doğrulandı): `lms unload` (C:\Users\dumba\.lmstudio\bin\lms.exe) / API `POST localhost:1234/api/v0/models/unload` (200) / UI Eject.
- **Bionic JIT (reload) AÇIK** — Local Model API → "Just-in-time model loading" (istek gelince modeli yükler); FunkGoth ekran görüntüsüyle teyit (Oturum 13). Mod B'nin reload adımı bu sayede otomatik. NOT: auto-unload/TTL hâlâ yok (#2423) → boşaltma manuel.
- **Ollama envanteri**: worker8b (5.2 GB), qwen3:8b (5.2 GB), gemma4:26b (18 GB). GPU testi için worker8b yeterli. Worker CPU eval ≈10.7 tok/s; **GPU solo ≈101.9 tok/s** (runs.csv 2026-10-02-03). JIT reload 27B ≈318s (LOG).
- **model-hafiza.md** (tohum: 27b ana / 8b worker / 26b aday) — runs.csv = ham veri, burası distille.