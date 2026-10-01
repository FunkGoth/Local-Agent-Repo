Layer Structure

Project

Unreal Engine Project EmberTale

Last updated

Sep 29, 2026

Summary

EmberTale katman hiyerarşisi — L0 Metodoloji, L1 Vizyon, L2 Proje İskeleti ve sistem sohbetlerinin bu katmanlarla ilişkisi



Details

Katman Yapısı:



L0 — Metodoloji: Bu sohbet. Nasıl çalışırız (süreç kuralları). Sabit kalır.

L0 Agent ve Araçlar: L0 numaralı ayrı sohbet; konusu AI agent'lar, Claude Code ve Unreal Engine skill'i. Konuşulanların ve kararların kaydı: agents-and-tools.md

L1 — Vizyon: Neden yapıyoruz (amaç/anlam).

L2 — Proje İskeleti: Ne yapıyoruz (sistem haritası, teknik yapı). Project Knowledge'daki dosya adı "L2-Proje-İskeleti".

L2 Asset Envanteri: L2 katmanına ait ayrı sohbet (L3 değil, sistem sohbeti de değil). Gerekçe: Proje İskeleti mimari plan, assetler bu planın inşasında kullanılan yapı malzemeleri. Kapsam: mevcut Blender assetlerinin (karakterler, kıyafet sistemi, rig, otomobiller, Unreal aktarım durumu) envanteri. Hafıza kaydı: topics/game-assets.md

Sistem sohbetleri (CarBP, InventoryBP gibi) bu üç katmanın altında, ayrı numaralandırma olmadan devam eder.

Vizyon Katmanı (L1):



Bu katman, Metodoloji (L0) ile Proje İskeleti (L2) arasında yer alır ve teknik sistemlerin ÜSTÜNDE, onları denetleyen bir referans katmanıdır. Proje İskeleti sohbeti "hangi sistemler, hangi teknik yapı" sorusuna cevap verirken, Vizyon sohbeti "oyunda neye ulaşmak istiyoruz, neden bu sistemi yapıyoruz" sorusuna cevap verir.

Üç katmanlı bilgi erişim modeli:

Proje Hafızasındaki areas/vision.md: küçük, özet nitelikli, her sohbette otomatik hazır bulunur. Şablonu ve detay seviyesi henüz kararlaştırılmadı (Vision sohbetinde belirlenecek).

Project Knowledge'daki "Project Vision" Sohbet Özeti: asıl kaynak (source of truth), detaylı içerik burada tutulur, kullanıcı tarafından güncellenip yüklenir.

Claude gerektiğinde kullanıcıdan detaylı analiz/açıklama talep edebilir — vision.md'deki özet yetersiz kaldığında bu yola başvurulur.

Bu katmanın amacı: bir sistemin DoD'si teknik olarak tamamlansa bile, vizyona hizmet edip etmediği sorgulanabilir kalsın.

