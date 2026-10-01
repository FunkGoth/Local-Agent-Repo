# E:\agent — Hafıza Protokolü (PROTOCOL)

## Amaç
Bu klasör, E:\ çalışma alanı için agent'ın oturumlar arası kalıcı hafızasıdır.
Agent burada özerk yazar; kullanıcı her an okuyup müdahale edebilir.
`E:\README.md` = her model/uygulama için evrensel giriş noktası (disk haritası + sabit kurallar).

## Dosya Haritası
| Dosya | Görev |
|---|---|
| PROTOCOL.md | İşleyiş kuralları (bu dosya). Sabit; değişikliği LOG'a gerekçesiyle yazılır. |
| STATE.md | Anlık durum — 4 sabit bölüm: Aktif İş / Sıradaki Adım / Beklenen Kararlar / Son Güncelleme. ≤30 satır, her adımdan sonra güncellenir. |
| TASKS.md | Sıra bekleyen işler. Sıradaki iş buradan seçilir. |
| DECISIONS.md | Çalışma alanı kararları (tarih, karar, gerekçe, karar veren). |
| LOG.md | Oturum günlüğü (ne oldu, neden). |
| lessons.md | Dersler — hata ya da işe yarayan yöntem, birer satır. |
| scratch/ | Geçici çalışma dosyaları (kullanıcı onayıyla silinebilir). |

## Oturum Başlangıcı
1. PROTOCOL.md → STATE.md → TASKS.md sırasıyla oku.
2. Yeni kullanıcı isteği varsa: önce TASKS.md'ye ekle, sonra işe başla.
3. STATE.md'deki "Sıradaki Adım" satırından devam et.

## Çalışma Döngüsü
- Al: TASKS.md'den sıradaki işi seç.
- Yap: işi küçük adımlarla ilerlet.
- Kaydet: her adımdan sonra STATE.md'ye ne yaptığını ve sıradaki adımı yaz.
- Öğren: hata yaptıysan veya işe yarayan bir yöntem bulduysan lessons.md'ye bir satır ekle.
- Ölç: her ~10 tool çağrısında kendine sor: "Tekrar ediyor muyum, talimatları unutuyor muyum, dosyalar çok mu okundu?" Evetse devir yap.
- Kapanış: iş bitince iş state.md'sine "TAMAM" yaz, TASKS.md Arşiv'e tek satır taşı; 30+ dosyaya ulaşan işi alt klasöre böl.
- Okuma bütçesi: aynı oturumda okuduğun dosyayı yeniden okuma; önce STATE.md, sonra yalnızca gerekli parça.
- Scratch: yalnızca geçici dosyalar; iş kapanışında "temizlenebilir mi?" sorusu + kullanıcı onayıyla silinir.
- Global dosya bütçesi: PROTOCOL+STATE+TASKS+DECISIONS+LOG+lessons toplamı ~300 satırı geçmesin; taşarsa detayı ilgili work/ iş dosyasına it.
- Distille: ayda 1 kez lessons.md'deki kalıcı dersleri PROTOCOL'e 1'er satır kural olarak al; lessons satırına "→ PROTOCOL'e alındı" işareti koy.

## Devir
- STATE.md'yi eksiksiz güncelle (durum, sıradaki adım, açık sorular).
- STATE'e "Beklenen Kararlar" satırlarını ekle — devir bu satırlar üzerinden kendiliğinden gerçekleşir (2026-10-01 devir testi doğruladı).
- "Yeni sohbet aç: PROTOCOL.md'yi oku ve devam et" yaz ve dur.

## Sabit Kurallar (değiştirilemez)
1. Yalnızca E:\ içinde yaz/sil.
2. Kullanıcı onayı olmadan silme / geri alınamaz işlem yok.
3. E:\agent\ git yedeği güncel tutulur — her önemli adımdan sonra commit.
4. Projenin Docs/ dosyaları kullanıcı malıdır: agent önerir, kullanıcı onaylar.
   Kullanıcı onayı olmadan hiçbir içerik "canon" ilan edilemez.

## Çoklu Proje İskeleti (2026-10-01)
- E:\ = agent'ın tahsisli çalışma alanıdır (çok proje + çok agent uygulaması + çok model için).
- `E:\README.md` = evrensel giriş noktası: kimlik, disk haritası, sabit kurallar, yeni proje iskeleti.
- Global system prompt'lar **proje-bağımsız** tutulur: boş ya da tek satır — "Workspace: E:\ — E:\README.md'yi oku ve uygula."
- Yeni proje = `E:\<proje-adı>\` kökü, standart iskelet: `README.md` (proje özeti) + `Docs\` (kullanıcı belgeleri) + `.agents\skills\` (proje skill'leri).
- Proje-özel talimatlar **her zaman projenin kendi klasöründe** yaşar; global prompt'a veya agent hafızasına taşınmaz.
- Agent hafızası (`E:\agent\`) proje-agnostiktir: çalışma sistemi + proje-arası durum; proje içerikleri içermez.
- Taşıma/yeniden yapılandırma: yalnızca `move` (silme yok); tüm referanslar (skill, doc, uygulama kökü) güncellenmeden taşınmaz; LOG'a gerekçeli kayıt.

## Protokol Değişiklikleri
- 2026-10-01 (Oturum 4): A1/A2 uygulandı — Çalışma Döngüsü'ne 5 kural (kapanış, okuma bütçesi, scratch, global dosya bütçesi, distille), STATE 4 bölüm/≤30 satır, Devir'e "beklenen karar" kuralı. Gerekçe: LOG.md.
- 2026-10-01 (Oturum 4): "Çoklu Proje İskeleti" bölümü + `E:\README.md` giriş noktası eklendi. Gerekçe: FunkGoth çok proje/çok uygulama/çok model planı; global prompt proje-bağımsız kalmalı. LOG.md.

## Proje Haritası (EmberTale)
- Proje kökü: E:\Project EmberTale\
- Kullanıcı belgeleri (taslak/canon statüsü onaya bağlı): Docs\ (VIZYON.md, ARCHITECTURE.md, DECISIONS.md, ROADMAP.md, Claude EmberTale md.zip)
- Proje skill'leri (her oturumda aktif): .agents\skills\
- Agent hafızası: E:\agent\ (bu klasör)
- Agent geçici alanı: E:\agent\scratch\
