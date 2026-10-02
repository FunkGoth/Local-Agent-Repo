# STATE — Anlık Durum
Son güncelleme: 2026-10-02 (Oturum 12)

## Aktif İş
| İş | Klasör | Durum |
|---|---|---|
| Kaynak bağımsızlığı + model geçişi | work/2026-10-02-… + model-hafiza.md | 🔄 **FAZ 1 v3: AMAÇ ONAYLI** (27B ana KALIYOR; hedef = paralel iş + verimlilik + kullanım hafızası + context ekonomisi); FAZ 1.5 testleri kuyrukta |
| Aşama 1 testi (VRAM attribüsyon + reload maliyeti) | work/…/cerceve.md | ✅ TAMAM: **VRAM DÜŞMEDİ (23.2→23.1 GB)**, TTFB 0.21s; attribüsyon çözüldü (dünki gözlem=util karışıklığı) |
| L1 boşta boşaltma (ONAYLI) | LM Studio ayarları | ✅ **ATTRİBÜSYON BİTTİ**: Bionic yaşam-döngüsü DEĞİL (dünki gözlem=util karışıklığı); 27B resident = tasarım (muaf #2051). Seçenekler: A=resident kabul / B=JIT+Idle TTL aç→boşalabilir (reload maliyeti ölçülecek) / C=hibrit → **FunkGoth seçecek** |
| Full veri toplama haftası | metrics/ | 🔄 2026-10-02→10-09: ~%50 full, hedef ≥3; hafta sonu rapor |
| Delegation Worker B | PROTOCOL + delegation/ | ✅ operasyonel; runs.csv 7 kayıt |
| Oto-devir (YENİ PARALEL İŞ) | agent/ + introspection | 🔄 kapsam netleşti (context izleme + otomatik devir); tasarım önerisi sunuldu → onay bekleniyor |
| Sistem Değerlendirmesi | metrics/ | ✅ Ö1+Ö2 ONAY+UYGULANDI; **Ö3 hâlâ açık**; son: 2026-10-02 |
| EmberTale T1–T6 | E:\Bionic\Project EmberTale\ | 🕐 ayrı sohbet |

## Sıradaki Adım (YENİ SOHBET — devir)
1. **L1 kaldıraç seçimi** (FunkGoth): A=resident kabul / B=JIT+Idle TTL aç + reload maliyeti ölçümü / C=hibrit.
2. **Oto-devir tasarısı onayı** — context izleme (introspection) + eşikler + devir paketi; Bionic'in context taşması davranışı sorulacak.
3. **(L1'de) Auto-unload durumu**: JIT=Auto-Load KAPALI; 27B manuel→muaf (#2051); B seçilirse JIT+Idle TTL açılır, reload maliyeti o zaman ölçülür.
4. **Veri haftası** (→10-09): policy'ye göre full (run_id çift; yeni iş tipi daima); hafta sonu rapor + mekanizma tartışması.
5. **FAZ 2** (10-09 sonrası): paralel kapasite ölçümü (2×GPU worker) + FAZ 2 raporu.
6. **Ö3**: sonraki Sistem Değerlendirmesinde ana model ~32 tok API örneği → baseline bandı.

## Beklenen Kararlar (FunkGoth)
- Aşama 1 attribüsyon: dünki düşüş anında ne yapıyordu? (yukarıdaki soru).
- **Oto-devir** konusu kapsamı + önceliği (yeni paralel iş).
- Auto-unload ayarını bulup soracak (cevap bu oturumda hazır: JIT=Auto-Load KAPALI; asıl=Idle TTL/Auto-Evict; ama 27B manuel→muaf #2051→asıl kaldıraç=Bionic).
- Mimari: **ONAYLI** (Oturum 11 kapanışı, c maddesi).

## Detay
- **Oturum 11 KAPANIŞI (devir)**: FunkGoth "bunlar olabilir mi?" (JIT+CORS ekran görüntüsü) + dürüst düzeltme: "Auto-Unload değil, **Auto-Load** olarak anladım". **DÜZELTME**: JIT=Auto-Load (KAPALI)≠boşaltma; asıl boşaltma=Idle TTL+Auto-Evict; CORS ilgisiz. **KRİTİK (#2051)**: JIT KAPALI→27B manuel yüklenen→muaf→**auto-unload ayarı 27B'yi boşaltmaz**; **asıl kaldıraç=Bionic model yaşam-döngüsü** (worker başlayınca VRAM düşüşü kanıtı). **a/b/c**: (a) Aşama 1 testi YAPILACAK ama DEVR (yeni sohbetin ilk işi); (b) FunkGoth auto-unload ayarını arıyor+soracak; (c) **mimari ONAYLI**. **YENİ PARALEL KONU: oto-devir** (TASKS Aktif'e eklendi).
- Oturum 11: FunkGoth 4 soruyu cevapladı + amacı genişletti: **27B ana model KALIYOR** (büyük tatminlik); hedef = paralel iş yeteneği + verimlilik + kullanım hafızası + context ekonomisi; boşta boşaltma ONAYLI (boşalan ~23.7 GB → alt ajanlar); model kütüphanesi planı (çeşitli model+kuantizasyon, benchmark beklenti önden). Gözlem: worker testinin (148 s) başladığı an GPU BELLEK 23→2–3 GB, KULLANIM %85–90→~%5. auto-unload KAPALI (boşta VRAM dolu).
- Çelişki notu (REVİZE): "auto-unload kapalı" iddiası Auto-Load/Unload karışıklığından; asıl bulgu: 27B manuel yüklenen→muaf (#2051). Worker başlayınca VRAM 23→2-3 GB düşmesi → tetikleyici büyük olasılıkla **Bionic yaşam-döngüsü**; Aşama 1 testi attribüsyonu + reload maliyetini ölçecek.
- Yeni dosya: `agent/model-hafiza.md` (tohum: 27b ana / 8b worker / 26b aday) — runs.csv = ham, burası distille.
- Worker: eval 7–10.4 tok/s (CPU); think:false 11x; kalite 5/5. Ana model ≈17 tok/s (ilk örnek).