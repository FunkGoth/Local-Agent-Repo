# STATE — Anlık Durum
Son güncelleme: 2026-10-02 (Oturum 14 — DEVİR)

## Aktif İş
| İş | Klasör | Durum |
|---|---|---|
| Kaynak bağımsızlığı + model geçişi | work/2026-10-02-… + model-hafiza.md | 🔄 FAZ 1 v3 ONAYLI (27B ana KALIYOR; hedef = paralel iş + verimlilik + context ekonomisi) |
| Aşama 1 testi | work/…/cerceve.md | ✅ BİTTİ: VRAM düşmedi, worker 27B ile koexist (runs.csv 2026-10-02-01) |
| L1 boşta boşaltma | Bionic (lms + JIT) | ✅ TEST BİTTİ: unload 23574→1500 MiB; 2×GPU worker 18.5/101.9 tok/s; JIT reload 317.7s; final 23082; **SIRA: W1 warm (GO) → 317.7s anomali tartışması** |
| Disk hızı ölçümü | work/…/disk-testi.md | ✅ BİTTİ (Oturum 14): model C:'de (980 PRO) 3.6 GB/s → 4.5s okuma; en yavaş disk (E:, 417 MB/s) bile ~38s → **317.7s DISK DEĞİL** |
| Full veri toplama haftası | metrics/ | 🔄 10-02→10-09: ~%50 full, hedef ≥3; hafta sonu rapor |
| Delegation Worker B | PROTOCOL + delegation/ | ✅ operasyonel; runs.csv 7 kayıt |
| Oto-devir | agent/ | ✅ KAPANDI (Oturum 12): mevcut devir yeterli, yeni mekanizma yok |
| Sistem Değerlendirmesi | metrics/ | ✅ Ö1+Ö2 uygulandı; Ö3 açık (son 10-02) |
| EmberTale T1–T6 | Project EmberTale/ | 🕐 ayrı sohbet |

## Sıradaki Adım (YENİ SOHBET — devir; FunkGoth sırası 3→2→1)
> **DEVİR (Oturum 14 sonu):** yeni sohbet → README → PROTOCOL → STATE; ilk iş = **2 W1 warm teyidi** (ONAYLI, GO bekleniyor) → ardından **1 317.7s anomali tartışması**.

1. ✅ **3 Disk testi** BİTTİ (2026-10-02): disk 317.7s'i açıklamıyor (model 4.5s okunur) → `work/…/disk-testi.md`.
2. **2 W1 warm teyidi** (ONAYLI, GO beklenir): worker8b GPU cold 18.5 → warm ~100 tok/s teyidi (~5 dk).
3. **1 317.7s anomali tartışması**: disk elendi; hipotezler = VRAM çakışması (2 worker VRAM'deyken reload) / istek→ilk-token wall-time / CPU-RAM thrashing.
4. **Mod B eşiği kararı** hâlâ açık (break-even ≈4300 output tok/batch; unattended kuralı?).
5. **Veri haftası** (→10-09) + **FAZ 2 raporu** (→10-09 sonrası) + **Ö3** (sonraki Sistem Değerlendirmesi).

## Beklenen Kararlar / Onaylar (FunkGoth)
- **Oturum 14 sırası**: 3 disk → 2 W1 warm → 1 anomali tartışması. Disk ✅; W1 warm ONAYLI (GO sırasıyla); anomali tartışması disk+warm sonrası.
- **Mod B eşiği** (break-even ≈4300 output tok/batch; unattended kuralı?) — hâlâ açık.
- Mimari ONAYLI (Oturum 11): GPU=1 kiracı; **Mod A** = interaktif (27B resident + workers CPU), **Mod B** = paralel batch (boşalt → 2×GPU worker → reload + entegre).

## Detay
- **Boşaltma yolları** (doğrulandı): `lms unload` / API `POST localhost:1234/api/v0/models/unload` (200) / UI Eject. **Bionic JIT (reload) AÇIK** (istek gelince yükler) → Mod B reload otomatik; auto-unload/TTL yok (#2423) → boşaltma manuel.
- **Ollama**: worker8b/qwen3:8b (5.2 GB), gemma4:26b (18 GB). Worker CPU ≈10.7 tok/s; **GPU solo ≈101.9 tok/s** (runs.csv 2026-10-02-03). JIT reload 27B ≈318s — **disk DEĞİL** (model 4.5s okunur).
- **Model**: `C:\LM-Models\lmstudio-community\Qwen3.8-27B-GGUF\Qwen3.8-27B-Q4_K_M.gguf` (15.66 GB, C: 980 PRO) + mmproj 0.87 GB. Disler: C 980 PRO (937 MB/s W), D VP4300L (1687 MB/s W), **E SPCC (323 MB/s W — yavaş, boş)**. model-hafiza.md = distille; runs.csv = ham veri.
