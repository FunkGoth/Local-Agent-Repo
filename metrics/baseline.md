# Sistem Baseline (2026-10-01 ölçüldü)
"Sistemin normalde nasıl" referansı — her Sistem Değerlendirmesinde yeniden örneklenir, fark varsa açıklanır.

| Metrik | Değer | Not |
|---|---|---|
| GPU | RTX 3090 24 GB (24576 MiB) | dedicated |
| VRAM (ana model yüklüyken) | 23704 MiB kullanımda / 619 boş | 27B Q4_K_M + 8192 budget; ~2 GB rezerv sert tavan |
| GPU kullanımı (oturum aktifken) | %96 | nvidia-smi anlık görüntüsü (ana model kullanımdayken) |
| RAM | 31.1 GB toplam / 12.8 GB boş | worker8b ≈ 6.7 GB resident → rahat sığar |
| Worker prefill | ~63 tok/s | run 2026-10-01-02 (7800X3D) |
| Worker eval | ~7.1 tok/s | run 2026-10-01-02; DDR5 bant genişliği tavanına yakın → thread/ayarla anlamlı iyileşme yok |
| Ana model (27B) tok/s | (ilk örneklenmede dolacak) | LM Studio server log'dan; salt okunur |

## Yorum kuralları
- Worker CPU'da → GPU'da beklenen etki: **sıfır** (VRAM delta ~0, ana model hızı değişmez). Sapma = anomali → ara.
- ~200 kelimelik worker çıktısı ≈ 45–60 sn (eval hız tavanı).
- Aynı anda TEK worker kuralı (VRAM kuralı) worker B'de zaten CPU ile garanti; A/C runtime'larında geçerli.
