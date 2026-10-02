# E:\Bionic\agent — Hafıza Protokolü (PROTOCOL)

## Amaç
Bu klasör, E:\Bionic\ çalışma alanı için agent'ın oturumlar arası kalıcı hafızasıdır.
Agent burada özerk yazar; kullanıcı her an okuyup müdahale edebilir.
`E:\Bionic\README.md` = her model/uygulama için evrensel giriş noktası (disk haritası + sabit kurallar).
Detaylı referanslar (delegation runtime, değerlendirme protokolü, deney arşivi): `E:\Bionic\agent\handbook.md` — **boot'ta OKUNMAZ**, gerektiğinde tam yol ile.

## Dosya Haritası
| Dosya | Görev |
|---|---|
| PROTOCOL.md | İşleyiş kuralları (bu dosya). Sabit; değişikliği gerekçesiyle LOG'a yazılır. |
| handbook.md | İhtiyaç hâlinde referans (delegation/ölçüm detayı, hız tablosu, deney arşivi, Bionic/LM Studio gerçekleri). Boot'ta okunmaz. |
| STATE.md | Anlık durum — 4 sabit bölüm: Aktif İş / Sıradaki Adım / Beklenen Kararlar / Son Güncelleme. ≤30 satır, her adımdan sonra güncellenir. |
| TASKS.md | Sıra bekleyen işler. Sıradaki iş buradan seçilir. |
| DECISIONS.md | Çalışma alanı kararları (tarih, karar, gerekçe, karar veren). |
| LOG.md | Oturum günlüğü — ARŞİV (boot'ta tam okunmaz). |
| lessons.md | Dersler — hata ya da işe yarayan yöntem, birer satır. |
| scratch/ | Geçici çalışma dosyaları (kullanıcı onayıyla silinebilir). |
| metrics/ | Veri havuzu (runs.csv, context-snapshots.csv, context-ledger.csv, eval-*.md, baseline.md). |

## Oturum Başlangıcı
1. PROTOCOL.md → STATE.md → TASKS.md sırasıyla oku.
2. Yeni kullanıcı isteği varsa: önce TASKS.md'ye ekle, sonra işe başla.
3. STATE.md'deki "Sıradaki Adım" satırından devam et.
4. **LOG.md = ARŞİV**: tam okuma yok; spesifik geçmiş olay → `search_file_line`. Çalışır durum kaynağı yalnız STATE.md.
5. **Arama kökü**: kullanıcı mesajında adı geçen disk yolu = birincil arama kökü (gerekirse önce erişim izni); orada yoksa kapsamı genişlet + LOG'a neden.

## Çalışma Döngüsü
- Al: TASKS.md'den sıradaki işi seç.
- Yap: işi küçük adımlarla ilerlet.
- Kaydet: her adımdan sonra STATE.md (K3 checkpoint: biten adımlar + açık dosya/bölüm + bekleyen sorular + sonraki somut eylem; test = yeni oturum yeniden okumadan devam edebilmeli).
- Öğren: hata / işe yarayan yöntem → lessons.md 1 satır.
- Ölç: her ~10 tool çağrısında: "Tekrar ediyor muyum, talimatları unutuyor muyum, çok mu okudum?" Evetse devir.
- Kapanış: iş state'ine "TAMAM", iş Arşiv'e; 30+ dosya → alt klasör.
- Okuma bütçesi: aynı oturumda okuduğunu yeniden okuma; önce STATE, sonra yalnız gerekli parça.
- Scratch: yalnızca geçici; kapanışta "temizlenebilir mi?" + onay.
- Global dosya bütçesi: PROTOCOL+STATE+TASKS+DECISIONS+lessons ~300 satır (LOG dışarıda — arşiv; LOG yumuşak tavan 300, 350'de FunkGoth'a sor, sessiz silme yok).
- Distille: ayda 1 kez kalıcı dersleri PROTOCOL'e 1 satır kural olarak al; lessons'a "→ PROTOCOL'e alındı" işareti.

## Devir
- STATE.md'yi eksiksiz güncelle (durum, sıradaki adım, açık sorular) + "Beklenen Kararlar" satırları — devir bu satırlar üzerinden kendiliğinden gerçekleşir.
- Devir mesajı: "Yeni sohbet aç: `E:\Bionic\agent\PROTOCOL.md`'yi oku ve devam et" (**TAM YOL** — O19 dersi: yolsuz isim sıfır-context oturumda çapasız arama göreviydi).

## Delegation (kompakt — detay: handbook.md)
- Ana agent = yönetici sohbet; ağır okuma/taslak/özet → worker (ayrı context havuzu). Worker'ın aracı YOK.
- VRAM kuralı (sert): 24 GB kart = aynı anda TEK worker; 27B + CPU worker (Mod A) koexist KANITLI (runs.csv 01, ana modele etki none).
- Brief → `delegation\brief-<konu>.md` (amaç + kapsam + gerekli dosya parçaları + çıktı formatı) → result ≤200 kelime → `result-<konu>.md`. Brief'e ZORUNLU: "çözülmüş/kapalı kararlar" + "reddedilen maddeyi yeniden önerme". Ana agent her result'ı doğrular.
- Runtime: **(A)** aynı model localhost:1234 — yalnız FunkGoth GO'su; **(B) VARSAYILAN**: worker8b (qwen3:8b) Ollama `http://localhost:11434/api/chat`, CPU-only, **`think:false`** (11x kanıt); **(C)** yeni Bionic sohbet = tam agent (FunkGoth açar).
- **Mod B (GPU batch)**: yalnız unattended; sıra = worker evict (`keep_alive:0`) → `lms unload` → 2×GPU worker → 27B JIT reload (VRAM boşken **17.44s**; çakışmada 317.7s = kuralın kanıtı). Break-even ≈200–250 output tok.
- Otonom sub-agent yetkisi: worker B çağrı başı onaysız tetiklenebilir (kaba tarama/özet/çıkarım/taslak); her run → runs.csv.
- **Routing (nihai 10-02)**: 27B = karar/diyalog/mimari/kısa okuma/dosya işi/doğrulama · worker8b CPU (Mod A) = 100+ satır özet/çıkarım/taslak (sıfır geçiş maliyeti) · Mod B = unattended paralel batch.

## Sistem Değerlendirmesi (kompakt — detay: handbook.md)
- 24s'de 1, **yalnızca FunkGoth onayıyla**; STATE "son_değerlendirme" >24s ise yeni oturumda kullanıcıya sor.
- Veri: runs.csv (light daima) + full örnekleme (yeni iş tipi ilk + ~1/3 rastgele + anomali sonrası mutlaka; **run <30s → light varsayılan** — nihai 10-02, kanıt run 04 %14 > %10).
- İçerik: hız/kalite trendi · ana model durumu · GPU/RAM etkileşimi · havuz kalitesi · ≤3 öneri → FunkGoth; karar → DECISIONS.
- Ö3: her değerlendirmede ana model ~32 tok API örneği → baseline.md bant.

## Context Ekonomisi (K1–K9) — NİHAİ 2026-10-02 (deney erken kapandı; 10-09 = gözden geçirme)
- **K1 KAL** (degradasyon sinyali): aynı oturumda (a) DEĞİŞMEDİĞİNİ BİLDİĞİN dosyayı yeniden okuma, (b) biten adımı yeniden yapma, (c) yol/kısıt karıştırma, (d) STATE çelişkisi, (e) **kullanıcı düzeltmesi** (agent hatayı üretti, kullanıcı yakaladı) → "compaction-degradasyon şüphesi" bildir + checkpoint + devir teklifi. Compaction sayacı YOK (agent sayamaz — sınırlılık).
- **K2 KAL** (çıktı hijyeni): varsayılan = `search_file_line` / dar satır aralığı / log kuyruğu; tam dosya okuması istisna + gerekçeli. **Batch**: aynı dosyaya çoklu düzenleme = tek çoklu-edit çağrısı; %30+ değişim = replace_file. **K2c (yeni)**: append-only CSV'ler (snapshots/ledger/runs) → **shell `>>`** (sıfır yansıma); edit_file yalnız yerinde düzeltmede.
- **K3 KAL** (checkpoint kalitesi): Çalışma Döngüsü / Kaydet.
- **K4 KAL**: 50+ satırlık üretilen çıktı → `work/` dosyası; context'e özet + yol.
- **K5 KAL**: bir prompt içinde K1 ≥1 kez tetiklendiyse → prompt bitiminde devir teklifi.
- **K6 DÜZELT** (ölçüm): **araç kullanan** her prompt sonunda (saf sohbet muaf) `introspection.read_session` → "Current context" → snapshots.csv; deg alanı + limitasyon (compaction tepe yakalanamaz) korunur; **yazım = shell `>>`, turn sonunda defterle TEK çağrıda (K2c)**.
- **K7 KAL**: 100+ satır okuma/özetleme → Worker B (Mod A varsayılan; küçük dosyalar muaf); ana agent doğrular.
- **K8 → K9'A BİRLEŞTİ**: edit yansıma (echo) maliyeti = defterin `tok_tahmini` sütunu; ayrı kural kaldırıldı (nihai 10-02).
- **K9 DÜZELT** (defter): kapsam = (a) 100+ satır okuma, (b) 50+ satır/tam dosya edit, (c) K ihlali; küçük pencere editleri muaf; **turn sonunda TEK shell append** (K2c); self-audit: "loglanacak olaylar defterde mi?" (K3 denetimi).
- **10-09 gözden geçirme**: `metrics/context-benchmark.md` kriterleriyle veri yeniden kontrol (yeni karar DEĞİL); rapor + FAZ 2 + Ö3 birlikte.

## Sabit Kurallar (değiştirilemez)
1. Yalnızca E:\Bionic\ içinde yaz/sil.
2. Kullanıcı onayı olmadan silme / geri alınamaz işlem yok.
3. E:\Bionic\agent\ git yedeği güncel tutulur — her önemli adımdan sonra commit.
4. Projenin Docs/ dosyaları kullanıcı malıdır; kullanıcı onayı olmadan hiçbir içerik "canon" ilan edilemez.

## Çoklu Proje İskeleti
- `E:\Bionic\README.md` = evrensel giriş noktası; global system prompt'lar proje-bağımsız (1 satır).
- Yeni proje = `E:\Bionic\<proje-adı>\` (README + Docs\ + .agents\skills\); proje-özel talimatlar her zaman projenin kendi klasöründe.
- Agent hafızası (`E:\Bionic\agent\`) proje-agnostiktir; proje içerikleri içermez.
- Taşıma = yalnızca `move` (silme yok); tüm referanslar güncellenmeden taşınmaz; LOG'a gerekçeli kayıt.

## Protokol Değişiklikleri
> Tam geçmiş: LOG.md (arşiv). Burada yalnız yapısal özet.
- 2026-10-01: çalışma döngüsü + STATE 4 bölüm + devir beklenen-karar + çoklu proje iskeleti + delegation/VRAM/brief/runtime + worker8b CPU + sistem değerlendirmesi + otonom sub-agent + light/full örnekleme + think:false.
- 2026-10-02 (O16/O18/O19): Mod B kuralı (evict→unload→JIT 17.44s kanıtı) + LOG=arşiv + devir TAM YOL + arama kökü + K1–K9 süreli deney.
- 2026-10-02 (O20, tam yetki): K1–K9 NİHAİ (K1/K2/K3/K4/K5/K7 KAL · K6/K9 DÜZELT · K8→K9 birleşti) + **K2c shell-append kuralı** + full örnekleme ~%50 override DÜŞÜRÜLDÜ (run<30s→light) + LOG bütçeden çıkarıldı + PROTOCOL distille (detay → handbook.md) + routing haritası nihai + deney 10-02'de erken kapandı (10-09 = gözden geçirme).

## Proje Haritası (EmberTale)
- Proje kökü: E:\Bionic\Project EmberTale\ (Docs\, .agents\skills\; T1–T6 ayrı sohbet)
- Agent hafızası: E:\Bionic\agent\ · Geçici alan: E:\Bionic\agent\scratch\
