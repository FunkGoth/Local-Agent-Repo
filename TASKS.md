# TASKS — Sıra
Son güncelleme: 2026-10-02 (Oturum 7 kapanışı: 3 onaylı madde TAMAM — skill çerçeveleme, B4 silme, ilk Sistem Değerlendirmesi)

## Aktif
- [2026-10-01] T7 Agent iyileştirme analizi: hafıza yönetimi + Bionic ayar/donanım taraması + model optimizasyonu → `work/2026-10-01-agent-iyilestirme-analizi/` (✅ rapor + B1–B6 kapalı; B4 TAMAM 2026-10-02; kalan: B6 manuel)
- [2026-10-01] Delegation Worker B: kurulum + ilk brief→result denemesi → `delegation/` (✅ worker8b CPU'da doğrulandı; T7 raporu incelemesi yapıldı; **Oturum 6: uçtan uca doğrulama PASS — CPU-only, hız, kalite; `think:false` varsayılan kabul edildi**)
- [2026-10-01] Sistem Değerlendirmesi: veri havuzu + günlük öz-değerlendirme → `metrics/` (✅ kurulum + genişletilmiş telemetri: VRAM shared, RAM commit, pagefile; capture.ps1; light/full örnekleme + ölçüm maliyeti takibi; **ilk değerlendirme TAMAM 2026-10-02 — eval-2026-10-02.md, 3 öneri FunkGoth'ya raporlandı**)

## Bekleyen
- [2026-10-02] **E:\Bionic\ kök migrasyonu** (Karar kayıtlı, **işlem onayı bekleniyor**): `E:\agent\` + agent'ın tüm çalışma dosyaları → `E:\Bionic\` altına; gerekçe: Ollama/Claude Code gibi harness'lar E:\'de kendi çalışma alanlarını kuracak. B6 (LM Studio global prompt) bu tartışmayla birlikte ele alınacak. Kapsam/harita taslağı: FunkGoth onayıyla.
> Not (2026-10-01, Oturum 3): T1–T6, FunkGoth'un `E:\Project EmberTale\` kökünde açacağı yeni sohbet içinde işlenecek.

- [2026-10-01] T1 [kullanıcı kararı] 4 doküman: elme yaklaşımı vs satır satır birlikte gözden geçirme.
- [2026-10-01] T2 [T1 sonrası, kullanıcı onayı] L1 vizyon canon'ı: onaylanan içeriğe göre özet (vision.md benzeri) + detay dosyası.
- [2026-10-01] T3 [L2 sohbeti] ARCHITECTURE.md / DECISIONS.md / ROADMAP.md'yi L2 Proje İskeleti sohbetinde işle (bu oturumda işlenmaz).
- [2026-10-01] T4 [T3 içinde] K001–K018 kararlarını kullanıcıyla teke teke doğrula; "karar veren" atıflarını düzelt.
- [2026-10-01] T5 [ilk sistem başlatılınca] DoD durum tablosunu doldurmaya başla (şu an "henüz sistem yok").

## Arşiv
- [2026-10-02] ✅ Miras kural talimatı (FunkGoth): ilke DECISIONS'a kaydedildi; skill "Proje Bağlamı" olarak yeniden çerçevelendi (90→76 satır); kurallaştırma RED
- [2026-10-02] ✅ T6/B4: scratch miras (4 dosya) + worker test (5 dosya) silindi (FunkGoth onayı)
- [2026-10-01] ✅ Hafıza Mimarisi — tamamlandı; devir testi Oturum 3'te başarılı (FunkGoth memnun). Klasör: `work/2026-10-01-hafiza-mimarisi/`
