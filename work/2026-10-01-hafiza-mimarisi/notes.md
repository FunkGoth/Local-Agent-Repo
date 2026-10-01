# Notes — Hafıza Mimarisi

## Tasarım Kararları
- work/ altındaki her işin adı: `YYYY-MM-DD-<kısa-ad>` (örn: `2026-10-01-hafiza-mimarisi`)
- Global STATE.md sadece POINTER: "aktif iş hangisi, ne aşamada" (10 satırı geçmez)
- İş özel state.md: o işin kendi "nerede kaldım"ı (detaylı)
- İş bittiğinde: state.md'ye "KAPANDI" + kapanış notu, klasör arşive geçer (silinmez)
- Çoklu aktif iş: STATE.md'de tablo, her satır bir iş

## Context Yönetimi Stratejisi
1. Context düşükken (< 20K): rahatça dosya oku, yaz
2. Context orta (20-50K): gereksiz dosya okuma, state.md'ye güven
3. Context yüksek (> 50K): devir yap → STATE.md güncelle → "yeni sohbet aç" de
4. Yeni oturum: PROTOCOL → STATE → work/<aktif>/state.md → devam

## Dikkat
- notes.md şişerse (> 200 satır) → böl: `notes-1.md`, `notes-2.md` veya alt klasör
- output/ klasörü: üretilen dosyalar için (kod, doküman, rapor). Geçici değil, iş çıktısı.
- scratch/ (E:\agent\scratch\): GEÇİCİ. İş bitince onayla sil.

## FunkGoth'un Teyit Edilecek Noktaları
- [ ] Yapıyı görüp onaylıyor mu?
- [ ] Klasör adlandırma formatı uygun mu?
- [ ] "Devir simülasyonu" yapılsın mı? (Bu sohbeti kapat, yenisinde sistemi takip et)
- [ ] EmberTale T1'e dönülse, work/2026-10-01-embertale-vizyon/ kurulacak mı?
