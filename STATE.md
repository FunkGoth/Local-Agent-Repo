# STATE — Anlık Durum (2026-10-02, Oturum 16 — 317.7s anomali ✅ KANITLI; sıradaki = Mod B eşiği kararı)

## Aktif İş
| İş | Klasör | Durum |
|---|---|---|
| Kaynak bağımsızlığı + model geçişi | work/2026-10-02-… + model-hafiza.md | 🔄 FAZ 1 v3 ONAYLI (27B ana KALIYOR; paralel iş + verimlilik + context ekonomisi) |
| Aşama 1 + L1 boşta boşaltma | work/…/cerceve.md | ✅ BİTTİ: koexist kanıtlı; unload 23574→1500; 2×GPU 18.5/101.9; JIT reload çalışıyor |
| W1 warm teyidi | metrics/ | ✅ BİTTİ (15): R1 cold 103.2 / R2 warm **108.1 tok/s** → ~100 TEYİT; W1 18.5 = kuyruk artifact'i (04/05) |
| Disk hızı ölçümü | work/…/disk-testi.md | ✅ BİTTİ: model 4.5s okunur → 317.7s DISK DEĞİL |
| 317.7s anomali | metrics/runs.csv 06 | ✅ **KANITLI (16)**: VRAM çakışması — VRAM boşken clean JIT reload **17.44s** vs worker VRAM'deyken 317.7s |
| Full veri haftası | metrics/ | 🔄 10-02→10-09: ~%50 full, hedef ≥3; hafta sonu rapor |
| Delegation Worker B | PROTOCOL + delegation/ | ✅ operasyonel; runs.csv 12 kayıt |
| EmberTale T1–T6 | Project EmberTale/ | 🕐 ayrı sohbet |

## Sıradaki Adım (Oturum 16)
1. **Mod B eşiği kararı (FunkGoth'ta)** — YENİ VERİYLE: gerçek geçiş maliyeti ≈**17–20s** (318s DEĞİL) → break-even ≈**200–250 output tok/batch** (eski ~4300). Seçenekler: (a) eşiğe tabi (b) unattended sürede her zaman B (c) başka. Kural adayı: **reload öncesi daima worker evict**.
2. Karar gelince: PROTOCOL/Delegation'a 1 satır kural + model-hafiza notu.
3. Veri haftası →10-09 (doğal akış); FAZ 2 raporu 10-09 sonrası; Ö3 devam.

## Beklenen Kararlar / Onaylar (FunkGoth)
- **Mod B eşiği**: ≈200–250 output tok/batch makul mu? unattended kuralı? + "reload öncesi worker evict" kuralı onayı.
- Mimari ONAYLI (Oturum 11): GPU=1 kiracı; **Mod A** = interaktif (27B resident + workers CPU), **Mod B** = paralel batch (evict → unload → 2×GPU worker → JIT reload ~17s + entegre).

## Detay
- **317.7s KANITI (Oturum 16, GO)**: worker8b evict (`keep_alive:0`, /api/ps boş) → `lms unload` (VRAM 23067→1038–1404 MiB) → 1. deneme `/api/v1` **404** (ama async JIT load tetiklendi) → 2. deneme `/api/v0/chat/completions` **http=200, 17.44s** (TTFB≈total, eval 16 tok ihmal), final **23368 MiB, oturum ayakta** (5. unload+reload; hiç kırılmadı). runs.csv 06.
- **Bionic API**: model yönetimi **`/api/v0/…`** (`/api/v0/models` 200; v1 chat 404); JSON `Content-Type` zorunlu (415 dersi). Endpoint yanlış bile olsa istek JIT load'u async tetikliyor.
- **W1 warm (Oturum 15)**: R1 cold fresh load 4.12s + 103.2 tok/s; R2 warm 108.1 tok/s. runs.csv 04/05.
- **Boşaltma**: `lms unload` / `POST localhost:1234/api/v0/models/unload` / UI Eject; **Bionic JIT reload AÇIK**. **Ollama worker8b**: CPU ≈10.7, GPU solo ≈102–108 tok/s; evict = `keep_alive:0`; GPU'da size_vram 6.3 GB.
- **Model**: `C:\LM-Models\…\Qwen3.8-27B-Q4_K_M.gguf` (15.66 GB, C: 980 PRO). Disk W: C 937 / D 1687 / E 323 MB/s. model-hafiza.md = distille; runs.csv = ham.
