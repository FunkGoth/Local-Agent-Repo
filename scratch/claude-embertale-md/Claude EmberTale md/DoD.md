DoD

Project

Unreal Engine Project EmberTale

Last updated

Sep 28, 2026

Summary

EmberTale Definition of Done (DoD) — kurallar, yaşam döngüsü, Hibrit Model ve tüm sistemlerin canlı DoD durum tablosu



Details

KURALLAR (sabit, nadiren değişir)

Definition of Done (DoD) Kuralları:



DoD, sistem üzerinde fiilen çalışılırken belirlenir (önceden toptan yazılmaz) — proje dinamik olduğu için önceden yazılan kriter hızla eskir.

DoD yaşam döngüsü: Doğuş (sistem sohbeti açılınca 3-5 maddelik ilk taslak) → Yaşam (yeni gereksinim çıkarsa: kapsama al = DoD'ye madde eklenir, veya backlog'a at = mevcut DoD değişmez) → Kapanış (tüm maddeler işaretlenince sistem "bitti" sayılır, -UI alt sohbeti açma izni doğar).

Madde formatı: her madde somut ve gözlemlenebilir bir davranış olarak yazılır (örn. "Gaz tuşuna basınca araç ileri gider"), öznel ifadeler kullanılmaz (örn. "araç kontrolü iyi çalışıyor" gibi ifadeler kabul edilmez).

Doğrulama kuralı: her madde, işaretlenmeden önce mutlaka PIE'de (Play In Editor) fiilen test edilir — sadece kod yazılmış olması yeterli değildir, gözle görülerek doğrulanmalıdır.

Kapanmış bir sisteme sonradan madde eklenirse: varsayılan olarak backlog'a gider; gerçekten kritikse sistem bilinçli olarak yeniden açılır.

DoD'nin Proje Hafızasına yazılan hali sadece güncel durumdur (maddeler, işaret durumu, backlog) — tartışma geçmişi yazılmaz.

DoD Hibrit Model — Sıkı Kural:



Detaylı madde listesi bu dosyada TUTULMAZ — sadece tek satır/sistem özet aşağıdaki tabloda tutulur. Madde bazında detay (hangi madde ne zaman doğrulandı, test notları) yalnızca o sistemin Sohbet Özeti dosyasında (Project Knowledge'da) yaşar.

OTOMATİK GÜNCELLEME TETİKLEYİCİSİ: Bir sohbette kullanıcı bir DoD maddesinin PIE'de doğrulandığını bildirdiğinde (kullanıcının fiili onayı gerekir — Claude kendi kendine tahmin edip işaretlemez), Claude bu bilgiyi ANINDA, kullanıcı ayrıca istemeden aşağıdaki tabloda günceller (yüzde ve tarih).

SOHBET ÖZETİ ÜRETİMİNDE DoD TAKİBİ: "Sohbet Özeti ver" komutu çalıştığında, Claude üretilen Sohbet Özetinin "Durum" bölümünü mevcut DoD ilerlemesine göre doldurur VE aşağıdaki tabloyu bu son duruma göre senkronize eder.

KAPASİTE KORUMASI: Bu dosya 40KB'a yaklaştığında (tavan \~49KB), Claude kullanıcıyı uyarır ve kapanmış/bitmiş sistemlerin tablo satırlarını arşivlemeyi (silmeyi — detay zaten Project Knowledge'da kalıcı) önerir. Yukarıdaki KURALLAR bölümü bu arşivlemeden etkilenmez, sabit kalır.

DURUM TABLOSU (canlı, sık güncellenir)

Sistem	Durum	Bağımlılık	Son Güncelleme

(henüz sistem yok)	—	—	—

Format: <SistemAdı> | <%X — N/M madde tamamlandı> | <bağımlı olduğu sistem, varsa> | <tarih>

