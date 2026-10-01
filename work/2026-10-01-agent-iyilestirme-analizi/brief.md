# İş: Agent İyileştirme Analizi (Hafıza + Bionic + Donanım)

## Ne?
FunkGoth isteği (Oturum 4):
1. Hafıza yönetimi iyileştirme önerileri (E:\agent protokolü: dosya yapısı, STATE/TASKS/LOG disiplini, work/ yaşam döngüsü)
2. Bionic mevcut ayarları + sistem donanımı taraması
3. Model'in mevcut donanım + agent ayarlarıyla optimize çalışması analizi (kanıta dayalı)

## Neden?
- EmberTale işine geçmeden önce kendi çalışma verimimi (hafıza, context, hız) daha iyi hale getirmek
- FunkGoth'a veriye dayalı, doğrulanabilir öneri sunmak (tahmin değil)

## Teslim
- rapor.md: donanım/ayar taraması + model analizi + öneriler (kendim değiştirebileceklerim / onay gerekenler)
- Onaylanırsa: E:\agent protokolünde iyileştirmeleri uygula + DECISIONS.md'ye karar kaydı

## Ortam (tarama 2026-10-01)
- OS: Windows 11 Pro (10.0.26300) | CPU: Ryzen 7 7800X3D (8C/16T) | RAM: 32 GB
- GPU: NVIDIA RTX 3090 (24 GB VRAM, Ampere) | Disk: 3× SSD (SATA 2TB, NVMe 1TB, NVMe 2TB)
- Model (LM Studio API :1234): qwen/qwen3.8-27b — quant'ı modeller klasöründen doğrulanacak
- Model tam context: 262K; KV F16 ≈ 64 KiB/token (262K → ~16 GiB, ağırlıklar üstüne)
- NVFP4 quant Blackwell'e özel → 3090 (Ampere) GGUF rotası

## Kim?
FunkGoth (onaylayıcı) + Agent (Bionic)

## Başlangıç
2026-10-01, Oturum 4
