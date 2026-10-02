# STATE — Anlık Durum (2026-10-02, Oturum 19 — boot tamam (BOOT HATASI analizi: LOG O19 + lessons; ÖNERİ: devir şablonu TAM YOL'lu → nihai karar oturumu); sıradaki adım: **nihai karar oturumu** (→10-09: K kararı + hafta sonu rapor))

## Aktif İş
| İş | Klasör | Durum |
|---|---|---|
| Kaynak bağımsızlığı + model geçişi | work/2026-10-02-… + model-hafiza.md | 🔄 FAZ 1 v3 ONAYLI (27B ana KALIYOR; paralel iş + verimlilik + context ekonomisi) |
| Aşama 1 + L1 boşta boşaltma | work/…/cerceve.md | ✅ BİTTİ: koexist kanıtlı; unload 23574→1500; 2×GPU 18.5/101.9; JIT reload çalışıyor |
| W1 warm teyidi | metrics/ | ✅ BİTTİ (15): R1 cold 103.2 / R2 warm **108.1 tok/s** → ~100 TEYİT; W1 18.5 = kuyruk artifact'i (04/05) |
| Disk hızı ölçümü | work/…/disk-testi.md | ✅ BİTTİ: model 4.5s okunur → 317.7s DISK DEĞİL |
| 317.7s anomali | metrics/runs.csv 06 | ✅ **KANITLI (16)**: VRAM çakışması — VRAM boşken clean JIT reload **17.44s** vs worker VRAM'deyken 317.7s |
| K1–K9 context ekonomisi + full veri | metrics/ + PROTOCOL | 🔄 BAŞLADI (18): ~%50 full (hedef ≥3); K9 defter (context-ledger.csv, 10-02'den); 1. gerçek veri: tepe 59k → compaction **−44.4k**; karar 10-09 (kriterler: context-benchmark.md) |
| Delegation Worker B | PROTOCOL + delegation/ | ✅ operasyonel; runs.csv 12 kayıt |
| EmberTale T1–T6 | Project EmberTale/ | 🕐 ayrı sohbet |

## Sıradaki Adım (Oturum 18)
1. **Nihai karar oturumu (FunkGoth: sonraki adım)**: veri toplama mekanizmasının faydası + multi-agent/CPU worker katkı/eksikler → nihai K kararları. Veri: context-ledger.csv (10-02'den aktif), context-snapshots.csv, runs.csv.
2. **Deney haftası disiplini →10-09**: K6 snapshot her prompt; K9 defter: 100+ satır okuma + her edit_file → ledger satırı (defterin kendi yazıları yazılmaz; turn sonu self-audit); K1 sinyal → devir teklifi; batch edit (aynı dosyaya çoklu düzenleme = tek çoklu-edit çağrısı); full örnekleme ~%50 (hedef ≥3).
3. FAZ 2 raporu (10-09 sonrası): paralel kapasite + iş tipi haritası + routing → Mod B metodunun verimliliği verilerle teyit edilecek.
4. Ö3 devam (ana model hız ölçümü).
5. **Sub-agent entegrasyon çalışması (HATIRLATMA — FunkGoth, Oturum 18)**: context-paralel teknikler + görev öncesi tahmin + iş→agent/model routing haritası; veri: runs.csv (BOŞLUK: 27B'de kendim yaptığım işler kayıtta YOK). Zaman: FunkGoth (muhtemelen 10-09 sonrası).

## Beklenen Kararlar / Onaylar (FunkGoth)
- Mimari ONAYLI (Oturum 11): GPU=1 kiracı; **Mod A** = interaktif (27B resident + workers CPU), **Mod B** = paralel batch (evict → unload → 2×GPU worker → JIT reload ~17s + entegre).
- **K1/K6 geçici varsayılanlar (agent, Oturum 18)**: K1 eşiği = ≥1 sinyal (yalnız DEĞİŞMEDİĞİ bilinen dosya okuması sayılır); K6 = prompt başına 1 snapshot (uzun işte başlangıç da). FunkGoth revize edebilir.
- **Tutarlılık notu (Oturum 18 devam)**: global dosya bütçesi (~300 satır) vs LOG=arşiv (LOG 226 satır) — öneri: LOG bütçeden çıkarılsın (arşiv bootstrap'ta okunmuyor); karara FunkGoth (nihai karar oturumu).

## Detay
- **Mod B kuralı (O16 ONAY (b))**: unattended her zaman B; GPU devir = önce `lms unload`; reload öncesi daima evict (`keep_alive:0`). Kanıt: worker VRAM'deyken 317.7s / boşken **17.44s** (runs.csv 06; detay LOG).
- **Bionic API + Boşaltma**: `/api/v0/…` (v1 404); JSON `Content-Type` zorunlu (415 dersi); yanlış endpoint bile JIT load'u tetikliyor; `lms unload` / UI Eject; JIT reload AÇIK. worker8b: CPU ≈10.7, GPU solo ≈102–108 tok/s; size_vram 6.3 GB.
- **Model**: `C:\LM-Models\…\Qwen3.8-27B-Q4_K_M.gguf` (15.66 GB, C: 980 PRO). Disk W: C 937 / D 1687 / E 323 MB/s. model-hafiza.md = distille; runs.csv = ham.
