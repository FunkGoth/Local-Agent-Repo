# STATE — Anlık Durum (2026-10-02, Oturum 18 — **K1–K7 context deneyi BAŞLADI (→10-09)** + full veri haftası (→10-09); 10-09'da: K kararı + hafta sonu rapor)

## Aktif İş
| İş | Klasör | Durum |
|---|---|---|
| Kaynak bağımsızlığı + model geçişi | work/2026-10-02-… + model-hafiza.md | 🔄 FAZ 1 v3 ONAYLI (27B ana KALIYOR; paralel iş + verimlilik + context ekonomisi) |
| Aşama 1 + L1 boşta boşaltma | work/…/cerceve.md | ✅ BİTTİ: koexist kanıtlı; unload 23574→1500; 2×GPU 18.5/101.9; JIT reload çalışıyor |
| W1 warm teyidi | metrics/ | ✅ BİTTİ (15): R1 cold 103.2 / R2 warm **108.1 tok/s** → ~100 TEYİT; W1 18.5 = kuyruk artifact'i (04/05) |
| Disk hızı ölçümü | work/…/disk-testi.md | ✅ BİTTİ: model 4.5s okunur → 317.7s DISK DEĞİL |
| 317.7s anomali | metrics/runs.csv 06 | ✅ **KANITLI (16)**: VRAM çakışması — VRAM boşken clean JIT reload **17.44s** vs worker VRAM'deyken 317.7s |
| Full veri haftası | metrics/ | 🔄 10-02→10-09: ~%50 full, hedef ≥3; hafta sonu rapor |
| K1–K7 context deneyi | metrics/ + PROTOCOL | 🔄 BAŞLADI (18): kurallar uygulandı; veri → context-snapshots.csv; karar 10-09 (kriterler: context-benchmark.md) |
| Delegation Worker B | PROTOCOL + delegation/ | ✅ operasyonel; runs.csv 12 kayıt |
| EmberTale T1–T6 | Project EmberTale/ | 🕐 ayrı sohbet |

## Sıradaki Adım (Oturum 18)
1. **K1–K7 deney haftası →10-09**: her prompt sonunda context snapshot (K6 → context-snapshots.csv); K1 sinyali → rapor + deg satırı; 10-09: K başına KAL/DÜZELT/TERK (context-benchmark.md; FunkGoth onayıyla).
2. Full veri haftası →10-09 (doğal akış: ~%50 full, hedef ≥3; hafta sonu rapor) — FunkGoth: metodun verimliliği hâlâ teste tabi.
3. FAZ 2 raporu (10-09 sonrası): paralel kapasite + iş tipi haritası + routing → Mod B metodunun verimliliği verilerle teyit edilecek.
4. Ö3 devam (ana model hız ölçümü).
5. **Sub-agent entegrasyon çalışması (HATIRLATMA — FunkGoth, Oturum 18)**: context-paralel teknikler + görev öncesi tahmin + iş→agent/model routing haritası; veri: runs.csv (BOŞLUK: 27B'de kendim yaptığım işler kayıtta YOK). Zaman: FunkGoth (muhtemelen 10-09 sonrası).

## Beklenen Kararlar / Onaylar (FunkGoth)
- Mimari ONAYLI (Oturum 11): GPU=1 kiracı; **Mod A** = interaktif (27B resident + workers CPU), **Mod B** = paralel batch (evict → unload → 2×GPU worker → JIT reload ~17s + entegre).
- **K1/K6 geçici varsayılanlar (agent, Oturum 18)**: K1 eşiği = ≥1 sinyal (yalnız DEĞİŞMEDİĞİ bilinen dosya okuması sayılır); K6 = prompt başına 1 snapshot (uzun işte başlangıç da). FunkGoth revize edebilir.

## Detay
- **Mod B kuralı (O16 ONAY (b))**: unattended her zaman B; GPU devir = önce `lms unload`; reload öncesi daima evict (`keep_alive:0`). Kanıt: worker VRAM'deyken 317.7s / boşken **17.44s** (runs.csv 06; detay LOG).
- **Bionic API + Boşaltma**: `/api/v0/…` (v1 404); JSON `Content-Type` zorunlu (415 dersi); yanlış endpoint bile JIT load'u tetikliyor; `lms unload` / UI Eject; JIT reload AÇIK. worker8b: CPU ≈10.7, GPU solo ≈102–108 tok/s; size_vram 6.3 GB.
- **Model**: `C:\LM-Models\…\Qwen3.8-27B-Q4_K_M.gguf` (15.66 GB, C: 980 PRO). Disk W: C 937 / D 1687 / E 323 MB/s. model-hafiza.md = distille; runs.csv = ham.
