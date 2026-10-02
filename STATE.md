# STATE — Anlık Durum
Son güncelleme: 2026-10-02 (Oturum 12)

## Aktif İş
| İş | Klasör | Durum |
|---|---|---|
| Kaynak bağımsızlığı + model geçişi | work/2026-10-02-… + model-hafiza.md | 🔄 **FAZ 1 v3 AMAÇ ONAYLI** (27B ana KALIYOR; hedef = paralel iş + verimlilik + hafıza + context ekonomisi) |
| Aşama 1 testi | work/…/cerceve.md | ✅ **BİTTİ**: VRAM düşmedi (23.2→23.1 GB), TTFB 0.21s; attribüsyon çözüldü (dün=util karışıklığı) |
| L1 boşta boşaltma | LM Studio ayarları | 🔄 **B ONAYLI (KOŞULLU)**: JIT+TTL+Auto-Evict AÇ → 27B boşalır, boşalan VRAM'i workers devralır (FunkGoth koşulu); sıradaki = ayarlar AÇ → reload ölçümü + 2×GPU worker testi |
| Full veri toplama haftası | metrics/ | 🔄 2026-10-02→10-09: ~%50 full, hedef ≥3; hafta sonu rapor |
| Delegation Worker B | PROTOCOL + delegation/ | ✅ operasyonel; runs.csv 7 kayıt |
| Oto-devir | agent/ | ✅ **KAPANDI (Oturum 12)**: mevcut devir sistemi (STATE/LOG + yeni sohbette otomatik çekme) yeterli — yeni mekanizma yok (FunkGoth) |
| Sistem Değerlendirmesi | metrics/ | ✅ Ö1+Ö2 uygulandı; **Ö3 açık**; son: 2026-10-02 |
| EmberTale T1–T6 | E:\Bionic\Project EmberTale\ | 🕐 ayrı sohbet |

## Sıradaki Adım (YENİ SOHBET — devir)
1. **L1-B uygulaması**: (a) FunkGoth LM Studio'da JIT + Idle TTL (10–15 dk önerim) + Auto-Evict AÇ; (b) teyit: 27B API ile boşaltma (`POST /api/v0/models/unload` — doğrulanacak) + evict; (c) **reload maliyeti ölçümü** (boşken TTFB); (d) **2×worker8b GPU paralel throughput** (FAZ 2 çekirdek testi, öne alınıyor).
2. **Veri haftası** (→10-09): policy'ye göre full (run_id çift; yeni iş tipi daima); hafta sonu rapor + mekanizma tartışması.
3. **Ö3**: sonraki Sistem Değerlendirmesinde ana model ~32 tok API örneği → baseline bandı.
4. **FAZ 2 raporu** (→10-09 sonrası): iş tipi haritası, routing, paralel kapasite, iş-başı öngörü (GPU worker verisi besleyecek).

## Beklenen Kararlar (FunkGoth)
- **L1: B (KOŞULLU) ONAYLI** — koşul: boşalan GPU'yu workers devralacak. TTL değeri (10–15 dk önerim) onaylanacak.
- Mimari: **ONAYLI** (Oturum 11 kapanışı, c maddesi).

## Detay
- **L1-B mimarisi**: GPU = paylaşılan kaynak, aynı anda 1 kiracı. **Mod A (interaktif)**: 27B resident + workers CPU (koexist kanıtlı, 10.7 tok/s). **Mod B (paralel batch)**: 27B işi böl → brief'ler → boşalt → workers GPU'da paralel → sonuçlar dosya → 27B geri + entegre. Bedel: mod başına 1 reload (~10–30 s tahmin, ölçülecek).
- #2051: JIT KAPALI → 27B manuel yüklenen → evict muaf. B'de 27B boşalabilir olmalı → ayar sonrası boşalmazsa: 1 kez boşalt, sohbet üzerinden yeniden yüklensin (manuel bayrağı).
- Oturum 12: Aşama 1 testi bitti (runs.csv 2026-10-02-01: worker8b CPU 13.3 s, eval 10.7 tok/s, PASS); Bionic yaşam-döngüsü hipotezi düştü; oto-devir KAPANDI; scratchpad test artıkları silindi (FunkGoth onayı).
- `agent/model-hafiza.md` (tohum: 27b ana / 8b worker / 26b aday) — runs.csv = ham, burası distille.
- Worker: eval 7–10.4 tok/s (CPU); think:false 11x; kalite 5/5. Ana model ≈17 tok/s (ilk örnek).
