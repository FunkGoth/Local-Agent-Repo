# STATE — Anlık Durum
Son güncelleme: 2026-10-02 (Oturum 11)

## Aktif İş
| İş | Klasör | Durum |
|---|---|---|
| Kaynak bağımsızlığı + model geçişi | work/2026-10-02-… + model-hafiza.md | 🔄 **FAZ 1 v3: AMAÇ ONAYLI** (27B ana KALIYOR; hedef = paralel iş + verimlilik + kullanım hafızası + context ekonomisi); FAZ 1.5 testleri kuyrukta |
| Aşama 1 testi (VRAM attribüsyon + reload maliyeti) | work/…/cerceve.md | 🕐 FunkGoth hazır olduğunda (O gözlemleyecek) |
| L1 boşta boşaltma (ONAYLI) | LM Studio ayarı + PROTOCOL | 🕐 FunkGoth auto-unload ayarının yerini arıyor; maliyet Aşama 1'de ölçülecek |
| Full veri toplama haftası | metrics/ | 🔄 2026-10-02→10-09: ~%50 full, hedef ≥3; hafta sonu rapor |
| Delegation Worker B | PROTOCOL + delegation/ | ✅ operasyonel; runs.csv 6 kayıt |
| Sistem Değerlendirmesi | metrics/ | ✅ Ö1+Ö2 ONAY+UYGULANDI; **Ö3 hâlâ açık**; son: 2026-10-02 |
| EmberTale T1–T6 | E:\Bionic\Project EmberTale\ | 🕐 ayrı sohbet |

## Sıradaki Adım
1. **Aşama 1 testi** (FunkGoth gözlemleyecek): boşta baz → worker başlat (GPU bellek+kullanım) → uyanma maliyeti (ilk token'a kadar reload). Çıktı: VRAM düşüşü attribüsyonu + L1 bedeli.
2. **L1 boşta boşaltma**: FunkGoth LM Studio auto-unload ayarını buluyor; açılsa → reload maliyeti + PROTOCOL "boşta faz" kuralı + worker GPU opsiyonu.
3. **Veri haftası** (→10-09): policy'ye göre full (run_id çift; yeni iş tipi daima); hafta sonu rapor + mekanizma tartışması.
4. **FAZ 2** (10-09 sonrası): paralel kapasite ölçümü (2×GPU worker denemesi) + FAZ 2 raporu; kütüphaneye model geldikçe model-hafiza'ya benchmark beklenti + test sonucu.
5. **Ö3**: sonraki Sistem Değerlendirmesinde ana model ~32 tok API örneği → baseline bandı.

## Beklenen Kararlar (FunkGoth)
- Aşama 1 testi zamanı (o gözlemleyecek) + auto-unload ayarı.
- FAZ 1 v3'teki mimari önerisi (GPU zaman paylaşımı, paralel kapasite tablosu, self-wake ara çözüm) onayı.

## Detay
- Oturum 11: FunkGoth 4 soruyu cevapladı + amacı genişletti: **27B ana model KALIYOR** (büyük tatminlik); hedef = paralel iş yeteneği + verimlilik + kullanım hafızası + context ekonomisi; boşta boşaltma ONAYLI (boşalan ~23.7 GB → alt ajanlar); model kütüphanesi planı (çeşitli model+kuantizasyon, benchmark beklenti önden). Gözlem: worker testinin (148 s) başladığı an GPU BELLEK 23→2–3 GB, KULLANIM %85–90→~%5. auto-unload KAPALI (boşta VRAM dolu).
- Çelişki notu: auto-unload kapalıyken worker testinde 27B boşaldı → tetikleyici kim? Aşama 1 testi çözecek.
- Yeni dosya: `agent/model-hafiza.md` (tohum: 27b ana / 8b worker / 26b aday) — runs.csv = ham, burası distille.
- Worker: eval 7–10.4 tok/s (CPU); think:false 11x; kalite 5/5. Ana model ≈17 tok/s (ilk örnek).