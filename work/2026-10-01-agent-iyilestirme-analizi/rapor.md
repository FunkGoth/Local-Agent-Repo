# Agent İyileştirme Analizi — Rapor
Tarih: 2026-10-01 · Durum: FunkGoth onayı bekleniyor · İş: `work/2026-10-01-agent-iyilestirme-analizi/`

## 1. Yönetici Özeti
- **Mevcut kurulum beklenenden iyi:** Qwen3.8-27B (Q4_K_M) RTX 3090'ta tam GPU offload + **MTP speculative decoding aktif** + 64K context slot ile çalışıyor. Yani hız için en önemli iki kaldıraçtan biri (spekülatif decode) zaten açık.
- **En kritik kalite kaldırağı:** Qwen3.8 serisinde KV cache quant'ı; topluluk verisi "en az Q8" diyor. Mevcut KV tipi logda görünmüyor (llama.cpp varsayılanı f16) → **LM Studio arayüzünden doğrulanmalı**.
- **Hafıza yönetimi çalışıyor** (devir testi başarılı) — 7 somut iyileştirme önerisi var; hepsi düşük maliyetli.

## 2. Donanım Taraması (2026-10-01)
| Bileşen | Değer | Not |
|---|---|---|
| CPU | AMD Ryzen 7 7800X3D (8C/16T, 4.2 GHz) | 96 MB L3 V-Cache → offload'e uygun |
| RAM | 32 GB | CPU offload için pay var |
| GPU | NVIDIA RTX 3090 — **24 GB VRAM** (Ampere, sürücü 57x) | NVFP4 (Blackwell) desteklemez → GGUF rotası |
| OS | Windows 11 Pro, 10.0.26300 (25H2) | — |
| Disk | 3× SSD: 2TB SATA · 980 PRO 1TB NVMe · VP4300L 2TB NVMe | Model indirme için alan geniş |

## 3. Bionic / LM Studio Ayar Taraması
| Ayar | Değer | Değerlendirme |
|---|---|---|
| Yüklü model | `qwen/qwen3.8-27b` (Q4_K_M, 16 GB GGUF, arch `qwen35` hybrid) | VLM (mmproj BF16, 889 MB) da yüklu |
| Context (oturum) | **64.000 token/slot** (n_slots=4, kv_unified) | Model max 262K; 64K dengeli |
| Global default context | 8192 (LM Studio `settings.json`) | Bionic oturum kendince üstünlük kılıyor; sorun yok |
| GPU offload | `n_gpu_layers=999999` (tamam GPU) | ✅ En iyi durum |
| Spekülatif decode | **MTP draft context aktif** (sunucu logu) | ✅ Hız kazancı |
| KV cache tipi | logda yok → varsayılan **f16** (doğrulanmalı) | f16/q8 için UI kontrolü |
| Thread | llama threadpool n_threads=6 | 16T CPU'ya göre düşük; ama darboğaz GPU, sorun değil |
| Diğer | `autoLoadBundledLLM=true`, `useLlamaCppEngineProtocolRuntime3=true`, guardrails `high/4GB`, speculative UI dahilı `false` | Bilgilendirme |

## 4. Model × Donanım Analizi
### 4.1 VRAM bütçesi (24 GB 3090)
| Senaryo | Ağırlık | KV (64K) | mmproj + pay | Toplam | Durum |
|---|---|---|---|---|---|
| **A — Mevcut**: Q4_K_M + 64K + f16 KV | ~16 GB | ~4 GB | ~2 GB | **~22 GB** | ✅ Sığıyor (mevcut durum) |
| B: Q5_K_M + 64K + q8 KV | ~19 GB | ~2 GB | ~2 GB | **~23 GB** | ⚠️ Çok sınırlı; kalite +, hız − |
| C: Q4_K_M + **128K** + q8 KV | ~16 GB | ~4 GB | ~2 GB | **~22 GB** | ✅ Sığar — uzun oturumlar için |
| D: Q6_K + 64K | ~22 GB | +4 GB | +2 GB | ~28 GB | ❌ Sığmaz → RAM offload gerekir (32 GB RAM var, hız düşer) |
| E: IQ4_XS (14.5 GB) + 64K + kvarn4 | ~14.5 GB | ~2 GB | ~2 GB | ~19 GB | ✅ Bol pay; topluluk: 16GB kartta 30–50 tok/s (32K–72K) |

### 4.2 Topluluk kanıtları (Qwen3.8-27B)
- KV cache ≈ **64 KiB/token** (f16); tam 262K context ≈ 16 GiB ağırlık üstüne.
- KV quant hassasiyeti: Qwen3.8 kullanıcıları **"en az Q8; Q4 thinking'i erken kesiyor"** diyor; bağımsız benchmark'lar farkın küçük (±1.8pp) olduğunu söylüyor. → **Güvenli liman: f16 veya q8; q4 önerilmez.**
- Thinking budget: bütçe reasoning ortasında bitince model "pes" ediyor → zor görevlerde reasoning effort yüksek tutulmalı.
- MTP (native speculative decoding) destekli → zaten aktif; hızın en önemli kazancı.
- NVFP4 quant (1.5× hız) yalnız Blackwell (RTX 50x) → bizim donanımda geçerli değil.

### 4.3 Öneri (öncelik sırasıyla)
1. **Mevcut A'yı koru** — Q4_K_M + 64K + MTP, bu donanım için sağlam ve hızlı bir denge.
2. **KV tipini doğrula** (LM Studio model ayarları): f16 veya q8 mi? q4 ise → **q8'e çek** (kalite için en yüksek getirili ayar).
3. **Uzun agent oturumları** (çok dosya + araştırma) için: ctx 64K → **128K** + q8 KV (senaryo C). Kısa sohbetlerde 64K yeterli.
4. **Kalite yükseltmesi (opsiyonel):** Q5_K_M indirmesi (~19 GB disk) — özellikle uzun akıl yürütme işlerinde; hız biraz düşer, VRAM payı daralır.
5. **Zor görevlerde reasoning effort'u artır** (Bionic ayarı) — thinking budget tükenmesine karşı.

## 5. Hafıza Yönetimi İyileştirmeleri (E:\agent)
**Çalışanlar (değiştirme):** devir testi ✅ · work/ iş klasörleri · git yedek · "Açık Sorular + Sıradaki Adım" kalıbı.

| # | Öneri | Değişiklik | Gerekçe |
|---|---|---|---|
| 1 | **STATE.md şablonu standart** | Sabit 4 bölüm: Aktif İş / Sıradaki Adım / Bekleyen Kararlar / Son Güncelleme + ≤30 satır | Devri tek dosya taşımalı; okuma maliyeti düşmeli |
| 2 | **TASKS.md'ye "son dokunma" tarihi** | Her satır `[YYYY-MM-DD]` öneki | Öncelik kararı: unutulmuş iş mi, taze iş mi |
| 3 | **work/ yaşam döngüsü kuralı** | Tamamlanınca: state.md'ye "TAMAM" + TASKS Arşiv'e tek satır; iş 30+ dosyaya ulaşırsa alt klasöre böl | Arşiv tıklanabilir kalsın, global dosyalar hafif |
| 4 | **lessons distille** | Ayda 1: lessons'daki tekrar eden ders → PROTOCOL'de 1 satır kural; lessons sadeleşsin | Ders birikince gürültü artıyor |
| 5 | **Okuma bütçesi** | Protokol'e: "Aynı oturumda okuduğun dosyayı yeniden okuma; önce STATE, sonra yalnızca gerekli parça" | Context ekonomisi (bu modelde 64K pay sınırlı) |
| 6 | **scratch/ kuralı** | scratch yalnızca geçici; her iş kapanışında "temizlenebilir mi?" sorusu + onayla sil | T6 gibi bekleyen birikimler olmasın |
| 7 | **Global dosya bütçesi** | PROTOCOL+STATE+TASKS+DECISIONS+LOG+lessons toplamı ~300 satırı geçmesin; taşarsa work/'e it | Her oturumda bunlar okunuyor = token maliyeti |

## 6. Aksiyon Listesi
### A — Kendim uygularım (onay gerekmez, kendi hafıza dosyalarım)
- A1: Öneri 1–5 ve 7 → PROTOCOL.md / STATE.md / TASKS.md güncellemesi (LOG'a gerekçeli kayıt)
- A2: Öneri 4'ün ilk turu: lessons'ı distille et (şu an 5 satır — erken ama kalıbı kur)

### B — FunkGoth onayı gerekir
- B1: ~~KV cache tipi doğrulama~~ → **ÇÖZÜLDÜ (2026-10-01): UI'da KV zaten Q8_0 (K+V), Flash Attention açık — değişiklik gerekmedi**
- B2: Context 64K → 128K (uzun agent oturumları için; 17.74 GB ağırlık ile ~23.5/24 GB, sınırda) *(token payı)*
- B3: ~~Q5_K_M indir~~ → **GERİ ÇEKİLDİ (2026-10-01): model dosyası 17.74 GB; Q5_K_M ≈ 21 GB + KV + mmproj → 24 GB VRAM'e sığmaz, katmanlar RAM'e taşar → yavaş**
- B4: `E:\agent\scratch\claude-embertale-md\` geçici kopyasını temizle (T6) *(silme)*
- B5: Zor görevler için reasoning effort ayarı (Bionic) *(davranış)*

## 7. Kaynaklar
- Sunucu logu: `C:\Users\dumba\.lmstudio\apps\bionic\server-logs\2026-10\2026-10-01.1.log` (MTP, n_gpu_layers, n_ctx_slot)
- LM Studio API: `localhost:1234` → `/v1/models`, `/api/v0/models` (quant, context)
- Bionic ayar: `C:\Users\dumba\.lmstudio\apps\bionic\settings.json`
- Topluluk: r/LocalLLaMA "KV cache may affect a lot in Qwen3.8-27B" ([1vsxca1](https://www.reddit.com/r/LocalLLaMA/comments/1vsxca1/)) · "KV quant vs Context" ([1vuqsj9](https://www.reddit.com/r/LocalLLM/comments/1vuqsj9/)) · "128K on 32GB GPU" ([1vojr6m](https://www.reddit.com/r/LocalLLaMA/comments/1vojr6m/)) · "16GB VRAM optimize guide" ([1vrchn9](https://www.reddit.com/r/LocalLLaMA/comments/1vrchn9/))
- Doküman: [unsloth.ai/docs/models/qwen3.8](https://unsloth.ai/docs/models/qwen3.8) · [recipes.vllm.ai (262K ctx doğrulaması)](https://recipes.vllm.ai/Qwen/Qwen3.8-27B)
- KV boyutu: [r/Qwen_AI (64 KiB/token)](https://www.reddit.com/r/Qwen_AI/comments/1vo8pjz/)
