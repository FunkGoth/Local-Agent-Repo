# TASKS — Sıra
Son güncelleme: 2026-10-03 (Oturum 26 — O26 onayları: isim ✅ + sessiz-stop ✅; canon + 3 kaldıraç ⏳; bookkeeping DEVR.md §2)

## Aktif
- [2026-10-03] **O26 — Project Optim onay turu 1**: isim ✅ "Project Optim" (alt SelfTune düştü) · sessiz-stop tespit mekanizması ✅ (`Docs\DATA-POLICY.md` §6 — sıradaki oturumdan itibaren aktif: oturum başında önceki oturum LOG kontrolü) · **⏳ canon**: açıklama verildi (Docs\=onaya dek TASLAK; canon = agent'ın belgeyi bağlayıcı kural olarak işlemesi; DATA-POLICY §5 statüsü) → FunkGoth kararı bekleniyor · **⏳ 3 kaldıraç**: ayar listesi verildi (1. reasoning effort: Bionic model ayarı — rutin=low/karar=high · 2. MTP: LM Studio'da kontrol — baseline.md'ye göre ZATEN AKTİF (draft 38/kabul 17) → teyit niteliğinde · 3. 128K: slot 4→2 + context 64K→128K, TEK AYARDA BİR; B2 reddi geçmişi + donma riski) → FunkGoth kararı bekleniyor.
- [2026-10-02] **O22 (devirde FunkGoth tarafından konuldu) — Unreal Engine projesi teorik oturum + devir soruşturması**:
  (a) **Teorik tartışma**: UE projesinde FunkGoth'un verdiği büyük işin, context dolması sebebiyle yarıda bırakılmadan ne kadarının tek oturumda yapılabileceği (veri: snapshots.csv 59k tepe + compaction vakaları, K2/K4 kuralları, worker'a delege etme).
  (b) **DEVRİ SORUŞTURMASI (devri alır almaz araştırmaya başla)**: "İşlem sırasında context dolması yüzünden durmam sebebiyle kaç defa kullanıcı benden yeni prompt ile kaldığım işe devam etmemi istedi?" → kaynaklar: LOG.md (compaction/devir satırları), context-snapshots.csv (delta<0 vakaları), sessions arşivi. **EĞER KAYIT BULUNAMAZSA VEYA DURUMLAR RAPORLANMADIYSA → FunkGoth'a açıkça belirt** (varsayıma düşme).
- [2026-10-02] **10-09 gözden geçirme** (O20'de planlandı): K verileri kriterlerle yeniden kontrol (`metrics/context-benchmark.md` arşiv + snapshots/ledger/runs.csv) + **FAZ 2 raporu** (paralel kapasite, iş tipi haritası, routing teyidi) + **Ö3** (ana model hızı yeniden örnekleme → `metrics/baseline.md` bant). · hazırlık denetimi hazır (O21: 50 veri noktası, K kriterleri anlık kontrol, boşluk listesi, yürütme sırası) → `metrics/`
- [2026-10-02] **Kaynak bağımsızlığı + model geçişi yeteneği** (AMAÇ ONAYLI — Oturum 11): 27B ana model KALIYOR. FAZ 1 (çerçeve) ✅ v3 + FAZ 1.5 (Aşama 1 ✅, L1 ✅) tamam. **FAZ 2 (→10-09 sonrası)**: paralel kapasite ölçümü (2×GPU worker) + rapor (iş tipi haritası, routing, paralel kapasite, iş-başı öngörü). **FAZ 3**: paralel ajan yönetimi. **Sub-agent entegrasyon çalışması (FunkGoth hatırlatma, Oturum 18)**: context-paralel teknikler + görev öncesi tahmin + iş→agent/model routing haritası (veri: runs.csv; BOŞLUK: 27B'de kendim yaptığım işler kayıtta yok). → `metrics/` + `delegation/` + `work/2026-10-02-kaynak-bagimsizligi-cercevesi/`

## Bekleyen
> Not (2026-10-01, Oturum 3): T1–T6, FunkGoth'un `E:\Bionic\Project EmberTale\` kökünde açacağı yeni sohbet içinde işlenecek.

- [2026-10-01] T1 [kullanıcı kararı] 4 doküman: elme yaklaşımı vs satır satır birlikte gözden geçirme.
- [2026-10-01] T2 [T1 sonrası, kullanıcı onayı] L1 vizyon canon'ı: onaylanan içeriğe göre özet (vision.md benzeri) + detay dosyası.
- [2026-10-01] T3 [L2 sohbeti] ARCHITECTURE.md / DECISIONS.md / ROADMAP.md'yi L2 Proje İskeleti sohbetinde işle (bu oturumda işlenmez).
- [2026-10-01] T4 [T3 içinde] K001–K018 kararlarını kullanıcıyla teke teke doğrula; "karar veren" atıflarını düzelt.
- [2026-10-01] T5 [ilk sistem başlatılınca] DoD durum tablosunu doldurmaya başla (şu an "henüz sistem yok").

## Arşiv
- [2026-10-02] ✅ **Nihai karar oturumu (O20, tam yetki)**: K1–K9 erken kapatıldı — K1/K2(+K2c)/K3/K4/K5/K7 KAL · K6/K9 DÜZELT · K8→K9 birleşti; full örnekleme override düşürüldü (run<30s→light); LOG bütçeden çıktı; PROTOCOL 92 satır + handbook.md (49) + DECISIONS/TASKS/STATE/LOG/lessons/benchmark güncel; **rapor: `work/2026-10-02-nihai-karar-oturumu/rapor.md`**
- [2026-10-02] ✅ **K1–K9 context ekonomisi + full veri haftası** (O18 açıldı, O20'de erken kapatıldı): veri = snapshots (8) + ledger (26) + runs (12); 59k tepe → compaction −44.4k; 2 GERÇEK deg=1; defter self-audit'i O18 batch ihlalini yakaladı — rapor yukarıda
- [2026-10-02] ✅ Aşama 1 testi (O12): VRAM düşmedi (23.2→23.1 GB), worker 27B ile koexist (runs.csv 01); TTFB 0.21s — `work/…/cerceve.md`
- [2026-10-02] ✅ L1 boşta boşaltma testi (O13): unload 23574→1500 MiB (kanıt); 2×worker8b GPU: W2 101.9 tok/s (warm solo) — `work/…/cerceve.md`
- [2026-10-02] ✅ Disk hızı ölçümü (O14): model 4.5s okunur → 317.7s DISK DEĞİL; E: 323 MB/s (SPCC) — `work/…/disk-testi.md`
- [2026-10-02] ✅ W1 warm teyidi (O15): warm **108.1 tok/s** teyitli; L1-B W1 18.5 = paralel kuyruk artifact'i — runs.csv 04/05
- [2026-10-02] ✅ Reload 317.7s anomali (O16): KANITLI = VRAM çakışması; clean JIT reload **17.44s** → kural: reload öncesi daima evict — runs.csv 06
- [2026-10-02] ✅ Mod B eşiği kararı (O16, ONAY (b)): unattended her zaman B, eşik yok + daima evict; metod verimliliği teste tabi →10-09 — PROTOCOL/Delegation + DECISIONS
- [2026-10-02] ✅ Oto-devir (O12): mevcut devir yeterli, yeni mekanizma gerekmez — PROTOCOL/Devir
- [2026-10-02] ✅ E:\Bionic\ kök migrasyonu (O9) — commit `97b7374`
- [2026-10-02] ✅ Miras kural talimatı: ilke DECISIONS'a; skill yeniden çerçevelendi (90→76 satır); kurallaştırma RED
- [2026-10-02] ✅ T6/B4: scratch miras (4 dosya) + worker test (5 dosya) silindi (FunkGoth onayı)
- [2026-10-01] ✅ Hafıza Mimarisi — devir testi O3'te başarılı — `work/2026-10-01-hafiza-mimarisi/`
- [2026-10-01] ✅ T7 Agent iyileştirme analizi: B1–B6 kapalı — `work/2026-10-01-agent-iyilestirme-analizi/`
- [2026-10-01] ✅ Delegation Worker B: kurulum + uçtan uca PASS (CPU-only, hız, kalite; `think:false` varsayılan) — `delegation/`
- [2026-10-01] ✅ Sistem Değerlendirmesi: veri havuzu + ilk değerlendirme 2026-10-02 — `metrics/`
</new_content>