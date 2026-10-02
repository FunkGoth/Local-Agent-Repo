# Kaynak Bağımsızlığı + Model Geçişi — Çerçeve (FAZ 1)

Başlangıç: 2026-10-02, Oturum 10 (FunkGoth: B seçimi — çerçeveyi şimdi kuralım, veriyi sonra dolduralım)
İlişkili iş: TASKS "Kaynak bağımsızlığı + model geçişi yeteneği"
Veri kaynakları: `metrics/runs.csv`, `metrics/baseline.md`, `metrics/eval-2026-10-02.md`, `delegation/`
Donanım: Ryzen 7 7800X3D / 32 GB RAM / RTX 3090 24 GB (baseline.md)

## 1. "Geçiş" Tanımı (taslak)
- Okuma: agent sistemi "bu makine + 24 GB kart"a bağlı kalmamalı. Her iş tipi için **bilinen minimum kaynak ihtiyacı** olmalı; iş, eldeki kaynağa göre yapılabilir/taşıyabilir olmalı.
- Kapsam (FunkGoth kararı bekleniyor):
  - (a) **Ana model** (Qwen 3.8 27B, GPU): başka/küçük/hızlı model — hangi kısıtla (kalite mi hız mı VRAM mi)?
  - (b) **Worker B** (qwen3:8b, CPU): daha küçük (4b — hızlı, zayıf) ya da daha büyük (14b — yavaş, güçlü)?
  - (c) **Platform/donanım**: bu makine → bulut / başka makine (tam "kaynak bağımsızlığı")?

## 2. Karar Kriterleri (taslak — FunkGoth onayı/düzeltmesi bekleniyor)
| Kriter | Öneri | Mevcut veri |
|---|---|---|
| Hız | iş tipine göre eval ≥ 4 tok/s (var olan aksiyon eşiği) + iş tipine wall-time hedefi (örn. özet < 60 s) | worker 7–10.4 tok/s; ana model ≈ 17 tok/s (ilk örnek; Ö3 bandı bekleniyor) |
| Kalite | FunkGoth onayı (şu an 5/5 accepted); iş tipine özel kriter tanımlanacak mı? | runs.csv quality + accepted kolonları |
| Kaynak | tahmin değil, ölçülmüş ayak izi: VRAM ded+shared delta, RAM commit delta, pagefile, GPU util | baseline.md + runs.csv full satırları (hedef ≥ 3) |
| Ana model | Ö3: her değerlendirmede ~32 tok API örneği → baseline bandı | ONAYLI (2026-10-02), uygulanacak |
| Maliyet | wall-time × iş sıklığı; capture_overhead_s izleniyor (>%10 → full oranı düş) | metrics/README politikası |

## 3. FAZ 2 Rapor İskeleti (veri haftası 10-09 sonrası doldurulacak)
1. **Envanter** — donanım, modeller, ölçülmüş ayak izleri (baseline.md)
2. **İş tipi haritası** — iş tipi → kaynak / süre / kalite (runs.csv)
3. **(a) Geçiş önerisi** — iş tipi bazında eşiği karşılayan en küçük model/boyut + geçiş yolu
4. **(b) Paralel kapasite** — VRAM hesabı: 24 GB'da aynı anda kaç worker + hangi iş tipleri paralelleştirilebilir
5. **(c) İş-başı kaynak öngörüsü** — "bu iş ~X tüketir; senin kalan kaynak durumun Y" (kullanıcının oyun/iş planı için)
6. **Bilinmeyenler / veri eksikleri** — hangisi ölçülememiş, neden

## Açık Sorular (FunkGoth)
1. Kapsam: (a) ana model, (b) worker, (c) platform — hangisi/hangileri?
2. Kalite eşiği: "onayın" yeterli mi, yoksa iş tipine kriter mi tanımlayalım?
3. Paralellik hedefi: bu makinede 2+ worker mı, yoksa 1 güçlü worker + gelecek donanım için hazırlık mı?

## Durum
- FAZ 1 (bu dosya): 🔄 AKTİF — soru cevapları bekleniyor
- FAZ 2 (veri + rapor): 🕐 veri toplama haftasına bağlı (→ 10-09)
