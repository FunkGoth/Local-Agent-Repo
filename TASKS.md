# TASKS — Sıra
Son güncelleme: 2026-10-02 (Oturum 11: Kaynak Bağımsızlığı FAZ 1 v3 AMAÇ ONAYLI — 27B ana KALIYOR; Aşama 1 testi + L1 boşta boşaltma kuyrukta)

## Aktif
- [2026-10-02] **Full veri toplama haftası** (Ö2 genişletilmiş, ONAYLI; **BAŞLADI 2026-10-02 — doğal akış, FunkGoth**): 2026-10-09'a kadar worker run'larında full örnekleme rastgele yoğun (~%50, run_id çift → full; yeni iş tipi daima full; hedef ≥3). Hafta sonu: veri raporu + ölçüm mekanizması optimizasyon tartışması. → `metrics/`
- [2026-10-02] **Kaynak bağımsızlığı + model geçişi yeteneği** (AMAÇ ONAYLI — Oturum 11): hedef = paralel iş yeteneği + verimlilik + kullanım hafızası + context ekonomisi; **27B ana model KALIYOR** (FunkGoth tatmini). **FAZ 1 (çerçeve) ✅ v3** → `work/2026-10-02-kaynak-bagimsizligi-cercevesi/` + `agent/model-hafiza.md` (tohum). **FAZ 1.5 (bu hafta)**: Aşama 1 testi + L1 boşta boşaltma (ONAYLI) + model-hafiza bakımı. **FAZ 2 (→10-09 sonrası)**: paralel kapasite ölçümü (2×GPU worker) + rapor (iş tipi haritası, routing, paralel kapasite, iş-başı öngörü) + kütüphane benchmark beklentileri. **FAZ 3 (ileride)**: paralel ajan yönetimi mekanizması (self-wake, işlenmiş veri akışı). → `metrics/` + `delegation/`
- [2026-10-02] **Aşama 1 testi** (FunkGoth gözlemleyecek, Oturum 11): 3 aşama — (1) boşta baz (VRAM dolu teyidi), (2) worker başlat: GPU bellek+kullanım izleme → VRAM düşüşü attribüsyonu, (3) uyanma maliyeti: ilk token'a kadar reload süresi → L1 bedeli. → `work/2026-10-02-kaynak-bagimsizligi-cercevesi/cerceve.md`
- [2026-10-02] **L1 boşta boşaltma** (ONAYLI — Oturum 11): LM Studio auto-unload ayarı aç (FunkGoth ayarın yerini arıyor) + timeout; reload maliyeti Aşama 1'de; kabul edilebilirse PROTOCOL'e "boşta faz" kuralı + worker GPU opsiyonu (2×8b GPU ≈ 11–13 GB sığar).
- [2026-10-02] **E:\Bionic\ kök migrasyonu** (ONAYLI): ✅ TAMAM — taşıma + yol referansları + commit `97b7374` (Oturum 9). Gerekçe: Ollama/Claude Code gibi harness'lar E:\'de kendi alanlarını kuracak.
- [2026-10-01] T7 Agent iyileştirme analizi: hafıza yönetimi + Bionic ayar/donanım taraması + model optimizasyonu → `work/2026-10-01-agent-iyilestirme-analizi/` (✅ rapor + B1–B6 kapalı; B4 TAMAM 2026-10-02; **B6 TAMAM 2026-10-02** — kullanıcı tek satır prompt'u uyguladı)
- [2026-10-01] Delegation Worker B: kurulum + ilk brief→result denemesi → `delegation/` (✅ worker8b CPU'da doğrulandı; T7 raporu incelemesi yapıldı; **Oturum 6: uçtan uca doğrulama PASS — CPU-only, hız, kalite; `think:false` varsayılan kabul edildi**)
- [2026-10-01] Sistem Değerlendirmesi: veri havuzu + günlük öz-değerlendirme → `metrics/` (✅ kurulum + genişletilmiş telemetri: VRAM shared, RAM commit, pagefile; capture.ps1; light/full örnekleme + ölçüm maliyeti takibi; **ilk değerlendirme TAMAM 2026-10-02 — eval-2026-10-02.md, 3 öneri FunkGoth'ya raporlandı**)

## Bekleyen
> Not (2026-10-01, Oturum 3): T1–T6, FunkGoth'un `E:\Bionic\Project EmberTale\` kökünde açacağı yeni sohbet içinde işlenecek.

- [2026-10-01] T1 [kullanıcı kararı] 4 doküman: elme yaklaşımı vs satır satır birlikte gözden geçirme.
- [2026-10-01] T2 [T1 sonrası, kullanıcı onayı] L1 vizyon canon'ı: onaylanan içeriğe göre özet (vision.md benzeri) + detay dosyası.
- [2026-10-01] T3 [L2 sohbeti] ARCHITECTURE.md / DECISIONS.md / ROADMAP.md'yi L2 Proje İskeleti sohbetinde işle (bu oturumda işlenmaz).
- [2026-10-01] T4 [T3 içinde] K001–K018 kararlarını kullanıcıyla teke teke doğrula; "karar veren" atıflarını düzelt.
- [2026-10-01] T5 [ilk sistem başlatılınca] DoD durum tablosunu doldurmaya başla (şu an "henüz sistem yok").

## Arşiv
- [2026-10-02] ✅ Miras kural talimatı (FunkGoth): ilke DECISIONS'a kaydedildi; skill "Proje Bağlamı" olarak yeniden çerçevelendi (90→76 satır); kurallaştırma RED
- [2026-10-02] ✅ T6/B4: scratch miras (4 dosya) + worker test (5 dosya) silindi (FunkGoth onayı)
- [2026-10-01] ✅ Hafıza Mimarisi — tamamlandı; devir testi Oturum 3'te başarılı (FunkGoth memnun). Klasör: `work/2026-10-01-hafiza-mimarisi/`
