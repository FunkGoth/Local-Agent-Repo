# Sistem Baseline (2026-10-01 ölçüldü)
"Sistemin normalde nasıl" referansı — her Sistem Değerlendirmesinde yeniden örneklenir, fark varsa açıklanır.

| Metrik | Değer | Not |
|---|---|---|
| GPU | RTX 3090 24 GB (24576 MiB) | dedicated |
| VRAM (ana model yüklüyken) | 23704–23770 MiB / 24576 (%96+) | 27B Q4_K_M + 8192 budget; tampon bölgesi zaten sınırlı |
| **VRAM shared (paylaşılan)** | **≈1113–1120 MB kullanımda** | Get-Counter `GPU Process Memory(*)\Shared Usage`; ana model + Bionic bu havuzdan paylaşımda | 
| GPU kullanımı (oturum aktifken) | %12–96 (dalgalı) | nvidia-smi; ana model aktivitesine göre |
| RAM commit | 49–51 GB / limit 71.1 GB | commit = RAM + pagefile havuzu |
| Pagefile (C:+D:) | **0 MB — boşta** | RAM şu an yeterli; baskıda ilk sıçrayacak yer | 
| RAM boş (dalgalanma) | 12.5–16 GB (2026-10-01, birkaç dk aralık) | ana model aktivitesine göre dalgalanır; worker ≈6.7 GB sığar |
| Ölçüm aracı | capture.ps1 ≈2 sn/çağrı | light çalıştırma = sıfır ek maliyet; full = 2 çağrı ≈4 sn |
| RAM | 31.1 GB toplam / 12.8 GB boş | worker8b ≈ 6.7 GB resident → rahat sığar |
| Worker prefill | ~63 tok/s | run 2026-10-01-02 (7800X3D) |
| Worker eval | ~7.1 tok/s | run 2026-10-01-02; DDR5 bant genişliği tavanına yakın → thread/ayarla anlamlı iyileşme yok |
| Ana model (27B) tok/s | (ilk örneklenmede dolacak) | LM Studio server log'dan; salt okunur |

## Yorum kuralları
- Worker CPU'da → GPU'da beklenen etki: **sıfır** (dedicated delta ~0, shared delta 0, ana model hızı değişmez). Sapma = anomali → ara.
- **Taşma (tampon aşımı) göstergesi**: çalışma sırasında `vram_shared` delta >0. Baseline shared ≈1.1 GB — delta bu zemin üzerinden okunur.
- ~200 kelimelik worker çıktısı ≈ 45–60 sn (eval hız tavanı).
- Aynı anda TEK worker kuralı (VRAM kuralı) worker B'de zaten CPU ile garanti; A/C runtime'larında geçerli.
