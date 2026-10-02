# STATE — Anlık Durum (2026-10-02, Oturum 15 — DEVİR; W1 warm BİTTİ, sıradaki = 317.7s anomali + Mod B eşiği)

## Aktif İş
| İş | Klasör | Durum |
|---|---|---|
| Kaynak bağımsızlığı + model geçişi | work/2026-10-02-… + model-hafiza.md | 🔄 FAZ 1 v3 ONAYLI (27B ana KALIYOR; paralel iş + verimlilik + context ekonomisi) |
| Aşama 1 + L1 boşta boşaltma | work/…/cerceve.md + Bionic (lms + JIT) | ✅ BİTTİ: koexist kanıtlı (01); unload 23574→1500; 2×GPU 18.5/101.9; JIT reload 317.7s; final 23082 (02/03) |
| W1 warm teyidi | metrics/ | ✅ **BİTTİ (Oturum 15)**: R1 cold 103.2 / R2 warm **108.1 tok/s** → warm ~100 TEYİT EDİLDİ; L1-B W1 18.5 = kuyruk artifact'i (04/05) |
| Disk hızı ölçümü | work/…/disk-testi.md | ✅ BİTTİ: model 4.5s okunur → **317.7s DISK DEĞİL** |
| Full veri haftası | metrics/ | 🔄 10-02→10-09: ~%50 full, hedef ≥3; hafta sonu rapor |
| Delegation Worker B | PROTOCOL + delegation/ | ✅ operasyonel; runs.csv 11 kayıt |
| Sistem Değerlendirmesi | metrics/ | ✅ Ö1+Ö2 uygulandı; Ö3 açık (son 10-02) |
| EmberTale T1–T6 | Project EmberTale/ | 🕐 ayrı sohbet |

## Sıradaki Adım (YENİ SOHBET — devir)
> **DEVİR (Oturum 15 sonu):** yeni sohbet → README → PROTOCOL → STATE; ilk iş = **1 317.7s JIT reload anomali tartışması** (güçlü hipotez: VRAM çakışması/kısmi CPU offload; doğrulama testi önerisi FunkGoth'ta) + **Mod B eşiği kararı** (≈4300 tok/batch) — ikisi de FunkGoth'ta bekliyor.

1. ✅ **3 Disk testi** (Oturum 14): disk 317.7s'i açıklamıyor → `work/…/disk-testi.md`.
2. ✅ **2 W1 warm teyidi** (Oturum 15): warm 103.2→108.1 tok/s; cold fresh load yalnız 4.12s.
3. **1 317.7s anomali tartışması** (ŞİMDİ): hipotezler = VRAM çakışması (2 worker VRAM'deyken reload) / istek→ilk-token wall-time / CPU-RAM thrashing; kanıt + karar önerisi.
4. **Mod B eşiği kararı** hâlâ açık (break-even ≈4300 output tok/batch) + **Veri haftası/FAZ 2 raporu** (→10-09) + **Ö3**.

## Beklenen Kararlar / Onaylar (FunkGoth)
- **Oturum 14 sırası**: 3 disk ✅ → 2 W1 warm ✅ (Oturum 15) → **1 anomali tartışması (sıradaki)**; **Mod B eşiği** (≈4300 tok/batch; unattended?) hâlâ açık.
- Mimari ONAYLI (Oturum 11): GPU=1 kiracı; **Mod A** = interaktif (27B resident + workers CPU), **Mod B** = paralel batch (boşalt → 2×GPU worker → reload + entegre).

## Detay
- **Boşaltma/reload**: `lms unload` / API `POST localhost:1234/api/v0/models/unload` / UI Eject; **Bionic JIT reload AÇIK**. **Ollama worker8b** (5.2 GB): CPU ≈10.7, **GPU solo ≈102–108 tok/s**; cold fresh load ≈4.1s; JIT reload 27B ≈318s — **disk DEĞİL**.
- **W1 warm detayı (Oturum 15)**: script `scratchpads/41/w1warm-test.ps1` (1. run: Set-Content -Value x2 hatası → script durdu, 27B boşaltılmadı; düzeltildi). Reload 1. deneme **http=415** = `curl -d` form content-type; `-H "Content-Type: application/json"` ile 200. Kapatma: worker keep_alive:0 evict (size_vram→0) + 27B geri, final 22800/24576 MiB.
- **Model**: `C:\LM-Models\lmstudio-community\Qwen3.8-27B-GGUF\Qwen3.8-27B-Q4_K_M.gguf` (15.66 GB, C: 980 PRO) + mmproj 0.87 GB. Disler: C 937 / D 1687 / **E 323 MB/s W (yavaş, boş)**. model-hafiza.md = distille; runs.csv = ham veri.
