# metrics — Performans Veri Havuzu
Kuruldu: 2026-10-01 (FunkGoth onayı: otonom sub-agent kullanımı + günlük sistem değerlendirmesi).
Sorumlu: ana agent (kayıt + değerlendirme); FunkGoth raporları okur, aksiyonları onaylar.

## Dosyalar
| Dosya | Görev |
|---|---|
| runs.csv | Worker çalıştırma kayıtları (append-only; 1 satır/çalıştırma — satır silinmez/değiştirilmez) |
| baseline.md | Sistem baseline'ı (VRAM/RAM/hız referansları); her Sistem Değerlendirmesinde yenilenir |
| eval-YYYY-MM-DD.md | Sistem Değerlendirmesi raporu (FunkGoth onayıyla üretilir, 24s'de ≤1) |

## runs.csv alanları
run_id (tarih-NN), date, task, model, runtime (B), prompt_tok, prompt_tps, eval_tok, eval_tps, duration_s, load_s,
ram_free_gb_before/after, vram_mb_before/after, gpu_util_before/after_pct,
main_model_impact (none/minor/major/unknown), quality_verdict (passed/minor-errors/failed), accepted (yes/no), notes

## Yakalama protokolü (her worker çağrısında)
1. **Önce**: `nvidia-smi --query-gpu=memory.used,utilization.gpu --format=csv` + boş RAM (PowerShell `Win32_OperatingSystem.FreePhysicalMemory`) → satıra yaz.
2. **Çağrı**: `curl -s -d @payload.json http://localhost:11434/api/chat` — yanıt JSON'ından: `prompt_eval_count`, `prompt_eval_duration`, `eval_count`, `eval_duration`, `load_duration`.
3. **Sonra**: nvidia-smi + boş RAM tekrar → satıra yaz. `main_model_impact`: VRAM delta <128 MB ve ana model çalışıyor → `none`; delta >256 MB veya ana model yavaşladı/kilitlendi → `major` (nedeni notes'a).
4. **Kalite**: ana agent result'ı doğrular → `quality_verdict` + `accepted`.
5. runs.csv'ye satır ekle + result dosyasının başlığına run_id yaz (izlenebilirlik).
6. git commit (E:\agent).

## Sistem Değerlendirmesi (24 saatte 1)
- **Tetik**: yalnızca FunkGoth onayı. STATE'te "son_değerlendirme" alanı; >24s ise yeni oturumda kullanıcıya sorulur.
- **Girdiler**: runs.csv (tümü) + LOG son bölüm + lessons + LM Studio server log örneği (ana model tok/s — salt okunur) + baseline.md.
- **Çıktı**: `eval-YYYY-MM-DD.md` — 5 bölüm: 1) Worker hız/kalite trendi · 2) Ana model durumu · 3) Etkileşim (GPU VRAM / RAM / CPU) · 4) Havuz kalitesi (eksik alan, ölçüm hatası) · 5) Öneriler (≤3, öncelikli) → FunkGoth'a rapor.
- **Aksiyon eşikleri (otomatik kontrol)**:
  - worker eval <4 tok/s → içerik/yük çakışması ara;
  - kalite "failed" ×2 aynı iş tipi → o iş tipini B'den çıkar (ana modele dön);
  - VRAM delta >256 MB → `num_gpu 0` ayarını doğrula;
  - aynı runs.csv alanı 3 kez boş → yakalama protokolünü düzelt.
- **Kapanış**: STATE "son_değerlendirme" güncellenir; öneriler FunkGoth'a raporlanır; git commit.
