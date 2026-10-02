# STATE — Anlık Durum (2026-10-02, Oturum 16 — devir alındı; sıradaki = 317.7s doğrulama testi GO + Mod B eşiği)

## Aktif İş
| İş | Klasör | Durum |
|---|---|---|
| Kaynak bağımsızlığı + model geçişi | work/2026-10-02-… + model-hafiza.md | 🔄 FAZ 1 v3 ONAYLI (27B ana KALIYOR; paralel iş + verimlilik + context ekonomisi) |
| Aşama 1 + L1 boşta boşaltma | work/…/cerceve.md | ✅ BİTTİ: koexist kanıtlı; unload 23574→1500; 2×GPU 18.5/101.9; JIT reload 317.7s; final 23082 (02/03) |
| W1 warm teyidi | metrics/ | ✅ BİTTİ (15): R1 cold 103.2 / R2 warm **108.1 tok/s** → ~100 TEYİT; W1 18.5 = kuyruk artifact'i (04/05) |
| Disk hızı ölçümü | work/…/disk-testi.md | ✅ BİTTİ: model 4.5s okunur → **317.7s DISK DEĞİL** |
| 317.7s anomali | work/…/cerceve.md | 🔄 tartışma açık: güçlü hipotez = **VRAM çakışması** (reload'da worker 6.3 GB VRAM'deydi) → doğrulama testi **GO bekleniyor** |
| Full veri haftası | metrics/ | 🔄 10-02→10-09: ~%50 full, hedef ≥3; hafta sonu rapor |
| Delegation Worker B | PROTOCOL + delegation/ | ✅ operasyonel; runs.csv 11 kayıt |
| EmberTale T1–T6 | Project EmberTale/ | 🕐 ayrı sohbet |

## Sıradaki Adım (Oturum 16)
1. **317.7s anomali — doğrulama testi GO** (FunkGoth'ta): worker evict + VRAM tamamen boş → `lms unload` → timed JIT reload. ~10–60s ise VRAM çakışması KANIT (→ Mod B protokolü: reload öncesi daima worker evict); ~5 dk ise CPU-side (thrashing/offload).
2. **Mod B eşiği kararı** (FunkGoth'ta): break-even ≈4300 output tok/batch — kural mı olacak? (a) eşiğe tabi (b) unattended sürede her zaman B (c) başka.
3. Veri haftası →10-09 (doğal akış); FAZ 2 raporu 10-09 sonrası; Ö3 devam.

## Beklenen Kararlar / Onaylar (FunkGoth)
- **Doğrulama testi GO**: 27B unload+reload; oturum riski düşük (13/15'te kırılmadı; final 23082/22800 MiB, oturum ayakta).
- **Mod B eşiği**: ≈4300 output tok/batch makul mu? unattended kuralı?
- Mimari ONAYLI (Oturum 11): GPU=1 kiracı; **Mod A** = interaktif (27B resident + workers CPU), **Mod B** = paralel batch (boşalt → 2×GPU worker → reload + entegre).

## Detay
- **Anomali kanıt zinciri**: disk elendi (C: 3.6 GB/s → 15.66 GB = 4.5s okuma); L1-B reload sırasında Ollama /api/ps: worker8b **size_vram=6.3 GB** → 6.3+23 > 24 GB → sığmaz → kısmi CPU offload → ilk-token wall-time şişmesi (317.7s). Alternatifler: istek→ilk-token kuyruk+decode; CPU-RAM thrashing (zayıf — RAM 32 GB, model RAM'e sığar).
- **W1 warm (Oturum 15)**: script `scratchpads/41/w1warm-test.ps1`; R1 cold fresh load **4.12s** + 103.2 tok/s; R2 warm 108.1 tok/s. JIT reload `curl -d` form content-type → **415**; `-H "Content-Type: application/json"` ile 200.
- **Boşaltma**: `lms unload` / `POST localhost:1234/api/v0/models/unload` (JSON header) / UI Eject; **Bionic JIT reload AÇIK** (Local Model API). **Ollama worker8b** (5.2 GB): CPU ≈10.7, **GPU solo ≈102–108 tok/s**; evict = `keep_alive:0`.
- **Model**: `C:\LM-Models\…\Qwen3.8-27B-Q4_K_M.gguf` (15.66 GB, C: 980 PRO) + mmproj 0.87 GB. Disk W: C 937 / D 1687 / **E 323 MB/s (SPCC, boş)**. model-hafiza.md = distille; runs.csv = ham.
</new_content>