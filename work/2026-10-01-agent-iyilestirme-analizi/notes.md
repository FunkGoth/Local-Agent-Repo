# Notes — Agent İyileştirme Analizi

## Donanım (tarama 2026-10-01)
- CPU: AMD Ryzen 7 7800X3D, 8 çekirdek/16 işparçacığı, max 4.2 GHz (96MB L3 V-Cache → CPU offload için uygun)
- RAM: 32 GB (31.1)
- GPU: NVIDIA GeForce RTX 3090 — 24 GB VRAM (Ampere; sürücü 32.0.16.1664 = 57x)
- Ek GPU: AMD Radeon (iGPU/display adapter)
- OS: Windows 11 Pro, 10.0.26300 (25H2)
- Disk: SPCC 2TB SATA SSD · Samsung 980 PRO 1TB NVMe · Viper VP4300L 2TB NVMe
- LM Studio API: localhost:1234 → `qwen/qwen3.8-27b`

## Araştırma notları (ilk tur)
- Qwen3.8-27B: tam context **262K**; KV F16 ≈ **64 KiB/token** → tam 262K ≈ **16 GiB** (ağırlıkların üstüne)
- KV quant: görüşler ikiye bölünmüş — Qwen3.8 kullanıcıları "en az Q8, Q4 thinking'i erken keser" diyor; bağımsız benchmark'lar KV quant'ın ±1.8pp'lik "non-event" olduğunu söylüyor. → Karar: minimum Q8 KV, mümkünse F16.
- Q6_K weights + q8 KV → tek 32GB kartta ~128K context. Bizim 24GB (3090) → quant/context/offload üçgeninde denge gerekli.
- NVFP4 (1.5× hız) yalnız Blackwell (RTX 50x) → 3090'ta GGUF rotası; Ampere'da Q5/Q6 GGUF + offload standart.
- Thinking budget: bütçe reasoning ortasında tükenirse model "pes" ediyor; zor işlere daha yüksek reasoning effort/budget gerekli.
- Kaynaklar: r/LocalLLaMA (1vsxca1, 1vuqsj9, 1vojr6m, 1vrchn9, 1vr4bs4), unsloth.ai/docs/models/qwen3.8, recipes.vllm.ai (262144 ctx doğrulanmış)

## Doğrulanacaklar
- [ ] Yerel model dosyası: isim, quant, boyut
- [ ] bionic/settings.json: context window, temperature, thinking budget
- [ ] config-presets içeriği
- [ ] LM Studio global ayarları (AppData/Roaming? hub/?)

## Hafıza yönetimi fikir havuzu (rapor için)
- STATE.md "Açık Sorular" + "Sıradaki Adım" devri tek başına taşıyor → bu kalıbı standartlaştır
- TASKS.md'de iş yaşlandıkça "son dokunma" tarihi eklenebilir (öncelik kararı için)
- lessons.md: aylık distille → PROTOCOL.md'ye kalıcı kural dönüşsün (artık 4 satır)
- work/ klasörleri: tamamlanınca state.md'ye "TAMAM" + arşiv etiketi; 30+ dosya varsa bölme kuralı
- Her oturumda E:\agent erişimi yeniden isteniyor → oturum başı tekrar maliyeti; not: yetkiler oturum başına (Bionic davranışı)
