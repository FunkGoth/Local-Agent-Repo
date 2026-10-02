# STATE — Anlık Durum
Son güncelleme: 2026-10-02 (Oturum 13)

## Aktif İş
| İş | Klasör | Durum |
|---|---|---|
| Kaynak bağımsızlığı + model geçişi | work/2026-10-02-… + model-hafiza.md | 🔄 FAZ 1 v3 ONAYLI (27B ana KALIYOR; hedef = paralel iş + verimlilik + context ekonomisi) |
| Aşama 1 testi | work/…/cerceve.md | ✅ BİTTİ: VRAM düşmedi, worker 27B ile koexist (runs.csv 2026-10-02-01) |
| L1 boşta boşaltma | Bionic (lms + JIT) | 🔄 **B ONAYLI (KOŞULLU)**: boşalt=`lms unload` (manuel) + reload=Bionic JIT AÇIK (teyitli); sıradaki = test onayı → unload → 2×GPU worker → reload ölçümü |
| Full veri toplama haftası | metrics/ | 🔄 10-02→10-09: ~%50 full, hedef ≥3; hafta sonu rapor |
| Delegation Worker B | PROTOCOL + delegation/ | ✅ operasyonel; runs.csv 7 kayıt |
| Oto-devir | agent/ | ✅ KAPANDI (Oturum 12): mevcut devir yeterli, yeni mekanizma yok |
| Sistem Değerlendirmesi | metrics/ | ✅ Ö1+Ö2 uygulandı; Ö3 açık (son 10-02) |
| EmberTale T1–T6 | Project EmberTale/ | 🕐 ayrı sohbet |

## Sıradaki Adım (YENİ SOHBET — devir)
1. **L1-B uygulaması**: (a) FunkGoth'tan test onayı (JIT teyitli: Local Model API → Just-in-time loading AÇIK → reload otomatik); (b) `lms unload` ile 27B boşalt + VRAM teyidi; (c) **2×worker8b GPU paralel throughput** (worker8b hazır — yeni model gerekmez); (d) **reload+TTFB ölçümü** (JIT); (e) runs.csv + rapor.
2. **Veri haftası** (→10-09): policy'ye göre full (run_id çift; yeni iş tipi daima); hafta sonu rapor + mekanizma tartışması.
3. **Ö3**: sonraki Sistem Değerlendirmesinde ana model ~32 tok API örneği → baseline bandı.
4. **FAZ 2 raporu** (→10-09 sonrası): iş tipi haritası, routing, paralel kapasite, iş-başı öngörü (GPU worker verisi besleyecek).

## Beklenen Kararlar (FunkGoth)
- **L1: B (KOŞULLU) ONAYLI** — koşul: boşalan GPU'yu workers devralacak. Sıradaki: unload→GPU testine onay.
- Mimari ONAYLI (Oturum 11): GPU=1 kiracı; **Mod A** = interaktif (27B resident + workers CPU), **Mod B** = paralel batch (boşalt → 2×GPU worker → reload + entegre).

## Detay
- **Bionic ≠ LM Studio (klasik)** — iki ayrı uygulama; bu makinede yalnız Bionic kurulu, 27B'yi Bionic yükler (port 1234 = Bionic.exe). Klasik LM Studio ayarları (JIT/TTL/Auto-Evict, #2051) Bionic'e UYGULANMAZ. Boşaltma yolları (doğrulandı): `lms unload` (C:\Users\dumba\.lmstudio\bin\lms.exe) / API `POST localhost:1234/api/v0/models/unload` (200) / UI Eject.
- **Bionic JIT (reload) AÇIK** — Local Model API → "Just-in-time model loading" (istek gelince modeli yükler); FunkGoth ekran görüntüsüyle teyit (Oturum 13). Mod B'nin reload adımı bu sayede otomatik. NOT: auto-unload/TTL hâlâ yok (#2423) → boşaltma manuel.
- **Ollama envanteri**: worker8b (5.2 GB), qwen3:8b (5.2 GB), gemma4:26b (18 GB). GPU testi için worker8b yeterli. Worker CPU eval ≈10.7 tok/s (runs.csv 2026-10-02-01).
- **model-hafiza.md** (tohum: 27b ana / 8b worker / 26b aday) — runs.csv = ham veri, burası distille.