# Kaynak Bağımsızlığı + Model Geçiş Stratejisi (FAZ 1)

Başlangıç: 2026-10-02, Oturum 10 (FunkGoth: B — çerçeveyi şimdi kuralım, veriyi sonra dolduralım)
v2 (2026-10-02): FunkGoth asıl isteğini netleştirdi → "Amaç" bölümü; kapsam L1–L4 kaynak stratejisine daraldı.
İlişkili iş: TASKS "Kaynak bağımsızlığı + model geçişi yeteneği"
Veri: `metrics/runs.csv`, `metrics/baseline.md`, `metrics/eval-2026-10-02.md`
Donanım: Ryzen 7 7800X3D / 32 GB RAM / RTX 3090 24 GB

## Amaç (FunkGoth'un isteği → benim anlama, onayı bekleniyor)
- Her iş için 27B maliyeti ödemek istemiyor: hafif iş → küçük model; 27B yalnızca gerçekten gerektiğinde.
- Asıl hedef: sistemi kullanıcıya rahatlatmak (oyun/iş) — 27B boşta durup ~23.7 GB VRAM işgal etmesin.
- Gözlem (FunkGoth): CPU worker'a geçince CPU load ve VRAM "ciddi şekilde azaldı" → attribüsyon açık (boşta-boşaltma mı, idle mı — ne gördüğü soruldu).
- "Bu sohbetin modelini değiştirme" zorunlu çerçeve DEĞİL — mümkün ama ikincil.

## Model Stoku (2026-10-02 API kontrolü)
| Model | Nerede | Boyut | Durum |
|---|---|---|---|
| qwen3.8-27b (Q4_K_M) | LM Studio :1234 | ~17.7 GB | **YÜKLÜ** (ctx 62208/262144) — ana model |
| qwen3:8b / worker8b (Q4_K_M) | Ollama :11434 | 5.2 GB | Worker B (CPU-only, num_gpu 0) |
| gemma4:26b (Q4_K_M) | Ollama :11434 | 18.7 GB | kullanılmıyor (27B ile aynı VRAM sınıfı → maliyet avantajı yok; kalite karşılaştırması adayı) |

## Mümkünlük Kontrolü (FunkGoth: "mümkünlüğü kontrol et")
1. **Bu sohbetin modelini değiştirme (ana model swap)**: harness düzeyinde mümkün (LM Studio load/unload; Bionic model alanı gönderiyor) — ama LM Studio'da yalnız 27b var. 8b'nin ana olması için yollar: (a) Ollama → LM Studio import denemesi, (b) Bionic'i Ollama'nın OpenAI-uyumlu ucuna çevirme (FunkGoth ayarı), (c) LM Studio'ya GGUF import (lessons: kırık).
   - Risk: 8b agentic güvenilirliği zayıf (T7 incelemesi: 2 sapma) → 8b ana = YALNIZCA hafif sohbetler (sohbet/kısa tek adımlı iş), tam agentic döngü değil.
   - Hafifletme: hafıza sistemi (E:\Bionic\agent\) tasarım gereği model-agnostik — model geçişi tam da onun için var.
2. **Boşta boşaltma**: 27B agent aktif değilken unload; talep gelince reload (maliyeti ölçülecek). Boşalan VRAM: ~23.7 GB.
3. **Routing**: zaten mevcut (delegation → worker8b), runs.csv ile ölçülüyor.

## Strateji Katmanları (öneri — öncelikler FunkGoth'ta)
- **L1 Boşta boşaltma** — en büyük bedava kazanım: kullanıcı oyun/iş yaparken VRAM'de 27B olmasın. (LM Studio auto-unload ayarı + reload maliyeti ölçümü)
- **L2 İş → model haritası** — runs.csv'den (FAZ 2 rapor §3a): 8b hangi işlerde kaliteyi geçiyor, hangilerinde 27B şart → kanıta dayalı routing. (veri haftası →10-09)
- **L3 Hafif sohbetlerde 8b ana** — opsiyonel; endpoint/import yolu denenecek; kalite kapısı L2 verisinden.
- **L4 İş-başı öngörü** — ağır işten önce: "bu iş ~X tüketir, ~Y süre; senin kalanın Z" (FAZ 2 rapor §5).

## FAZ 2 Rapor İskeleti (veri haftası 10-09 sonrası doldurulacak)
1. Envanter — donanım, modeller, ölçülmüş ayak izleri (baseline.md)
2. İş tipi haritası — iş tipi → kaynak / süre / kalite (runs.csv)
3. (a) Geçiş/routing önerisi — iş tipi bazında eşiği karşılayan en küçük model + yol
4. (b) Paralel kapasite — 24 GB'da aynı anda kaç worker
5. (c) İş-başı kaynak öngörüsü
6. Bilinmeyenler / veri eksikleri (FunkGoth'un VRAM/CPU gözleminin attribüsyonu dahil)

## Karar Kriterleri (taslak)
| Kriter | Öneri | Mevcut veri |
|---|---|---|
| Hız | iş tipine eval ≥ 4 tok/s (var olan eşik) + wall-time hedefi (örn. özet < 60 s) | worker 7–10.4; ana ≈ 17 tok/s (ilk örnek; Ö3 bandı bekleniyor) |
| Kalite | FunkGoth onayı (5/5 accepted) + iş tipine kriter tanımlanacak mı | runs.csv accepted kolonu |
| Kaynak | tahmin değil ölçülmüş ayak izi (VRAM delta, RAM commit, pagefile) | baseline.md + full satırları (hedef ≥ 3) |
| Ana model | Ö3: her değerlendirmede ~32 tok örneği → baseline bandı | ONAYLI, uygulanacak |
| Maliyet | wall-time × iş sıklığı; capture_overhead_s takibi | metrics/README politikası |

## Açık Sorular (FunkGoth)
1. Anlayışım doğru mu? (hedef = oyun/iş sırasında VRAM rahatlasın / agent daha ucuz-hızlı / ikisi birden?)
2. Ne gördün tam olarak — Task Manager'da GPU BELLEK mi düştü yoksa GPU KULLANIM (utilization) mı? LM Studio stats mı?
3. LM Studio'da "boşta modeli boşalt" (auto-unload) ayarı açık mı?
4. Katman önceliği: L1 > L2 > L3 mi?

## Durum
- FAZ 1 (bu dosya): 🔄 v2 — FunkGoth cevapları bekleniyor
- FAZ 2 (veri + rapor): 🕐 veri toplama haftası (→ 10-09)
