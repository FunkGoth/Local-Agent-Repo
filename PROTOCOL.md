# E:\Bionic\agent — Hafıza Protokolü (PROTOCOL)

## Amaç
Bu klasör, E:\Bionic\ çalışma alanı için agent'ın oturumlar arası kalıcı hafızasıdır.
Agent burada özerk yazar; kullanıcı her an okuyup müdahale edebilir.
`E:\Bionic\README.md` = her model/uygulama için evrensel giriş noktası (disk haritası + sabit kurallar).

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
| metrics/ | Performans veri havuzu (runs.csv + eval-*.md raporları + baseline.md) — Sistem Değerlendirmesi girdisi. |

## Oturum Başlangıcı
1. PROTOCOL.md → STATE.md → TASKS.md sırasıyla oku.
2. Yeni kullanıcı isteği varsa: önce TASKS.md'ye ekle, sonra işe başla.
3. STATE.md'deki "Sıradaki Adım" satırından devam et.
4. **LOG.md = ARŞİV** (FunkGoth, 2026-10-02): oturum başlangıcında TAM OKUNMAZ; spesifik geçmiş olay gerekiyorsa `search_file_line`. Çalışır durum kaynağı yalnız STATE.md.

## Çalışma Döngüsü
- Al: TASKS.md'den sıradaki işi seç.
- Yap: işi küçük adımlarla ilerlet.
- Kaydet: her adımdan sonra STATE.md'ye ne yaptığını ve sıradaki adımı yaz. Checkpoint kriteri (K3): biten adımlar + açık dosya/bölüm + bekleyen sorular + bir sonraki somut eylem; test = yeni oturum yalnızca STATE + iş dosyasıyla, yeniden okumadan devam edebilmeli.
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

## Delegation (context havuzu yönetimi) — 2026-10-01
- Ana agent = yönetici sohbet (bu tip); ağır okuma/taslak/özet/çıkarım worker agent'lara delege edilir (ayrı context havuzları).
- VRAM kuralı (sert): 24 GB kartta **aynı anda TEK worker** — ikinci aktif context ≈ +2.2 GB; 2026-10-01 donması (23.6/24 GB) 2×64K slot'un sığmadığını kanıtladı.
- Brief protokolü: iş → `delegation\brief-<konu>.md` (amaç + kapsam + GEREKLİ dosya parçaları + çıktı formatı/kelime limiti) → worker çıktısı **kompakt** (≤200 kelime veya sabit format) → `delegation\result-<konu>.md`.
- Ana agentın context'i yalnız brief+result kadar büyür; hacimli ara iş atılabilir havuzda kalır.
- Worker'ın aracı YOK: sadece "beyin" işi (taslak, review, özet, çıkarım); dosya işini ana agent yapar.
- Worker runtime'ları: **(A)** aynı model, localhost:1234 API — sıfır kurulum, SINIRDA (yalnız FunkGoth GO'suyla); **(B) KURULU (2026-10-01)**: `worker8b` (qwen3:8b, 5.2 GB) — Ollama port 11434, **CPU-only** (`num_gpu 0`, `num_ctx 8192`, `temperature 0.3`), endpoint `http://localhost:11434/api/chat`; **(C)** yeni Bionic sohbet = tam agent, araçlı (FunkGoth açar, brief'i ben yazarım).
- Worker B çağrısı: `curl -s -d @payload.json http://localhost:11434/api/chat` (`model: worker8b`, `stream: false`, **`think: false`** = varsayılan — 2026-10-01 ölçümü: aynı brief 41s→3.7s, kalite korundu; derin çıkarım işinde `think: true`); worker'ın aracı YOK → gerekli dosya parçaları prompt'a gömülür.
- **Otonom sub-agent yetkisi (FunkGoth, 2026-10-01)**: ana agent, verimli gördüğü işlerde worker B'yi çağrı başı onay sormadan tetikler (kaba tarama/özet/çıkarım/taslak); her çalıştırma `metrics/runs.csv`'ye kaydedilir (yakalama protokolü → metrics/README.md). Runtime C hâlâ FunkGoth GO'su ister (ana model context/VRAM tüketir).
- **Mod B kuralı (FunkGoth onayı, 2026-10-02)**: GPU worker'a devir = önce `lms unload` (Mod B); unattended batch için eşik yok (gerçek geçiş maliyeti ~17–20s, Oturum 16 KANITI). **Reload öncesi daima worker evict (`keep_alive:0`)** — VRAM'de worker varken JIT reload 317.7s (çakışma); VRAM boşken 17.44s (runs.csv 06). Metod verimliliği hâlâ teste tabi: veri toplama (→10-09) sürer.

## Sistem Değerlendirmesi (24 saatte 1) — 2026-10-01
- Veri havuzu: `metrics/` — runs.csv (append-only, her worker çalıştırması 1 satır) + `eval-YYYY-MM-DD.md` raporları + baseline.md.
- Yakalama: **light** (her çalıştırmada, sıfır ek maliyet: API metrikleri + kalite) / **full** (örnek: VRAM dedicated+shared, RAM free/avail/commit, pagefile — capture.ps1). Örnekleme: yeni iş tipinin ilk çalıştırması + ~1/3 rastgele + anomali sonrası mutlaka. Ölçüm maliyeti (capture_overhead_s) izlenir ve raporlanır; >%10 ise full oranı düşürülür.
- Tetik: 24 saatte 1, **yalnızca FunkGoth onayıyla**; STATE'teki "son_değerlendirme" >24s ise yeni oturumda kullanıcıya sorulur.
- İçerik: worker hız/kalite trendi · ana model durumu (server log örnekleri) · GPU/RAM etkileşimi · havuz kalitesi · ≤3 öncelikli öneri → FunkGoth'a rapor.
- Aksiyon eşikleri: eval <4 tok/s → ara; kalite "failed" ×2 aynı iş tipi → o iş tipini B'den çıkar; VRAM delta >256 MB → num_gpu 0 doğrula.
- Gerekçe: FunkGoth (2026-10-01) sub-agent otonom kullanım + günlük veri tabanlı öz-değerlendirme yetkisi verdi (tetik onaylı).

## Context Ekonomisi & Devir Kalitesi (K1–K7) — 2026-10-02, SÜRELİ DENEY (→10-09)
FunkGoth onaylı (Oturum 18). Süreli deney: veri (metrics/context-benchmark.md + context-snapshots.csv) faydayı göstermezse veya maliyetliyse TERKEDİLİR (karar 10-09).
- K1 (degradasyon sinyali): aynı oturumda (a) DEĞİŞMEDİĞİNİ BİLDİĞİN bir dosyayı yeniden okuma, (b) biten adımı yeniden yapma, (c) yol/kısıt karıştırma, (d) STATE.md ile çelişme → derhal "compaction-degradasyon şüphesi" bildir + checkpoint öner + devir teklif et. Compaction SAYACI YOK (agent compaction'ı sayamaz — sınırlılık).
- K2 (çıktı hijyeni): varsayılan = `search_file_line` / dar satır aralığı / log kuyruğu (son N satır + hatalar); tam dosya okuması istisna ve gerekçeli.
- K4 (büyük üretim): 50+ satırlık üretilen çıktı (rapor/analiz/tablo) → `work/` dosyası; context'e özet + yol.
- K5 (prompt sınırı): bir prompt içinde K1 ≥1 kez tetiklendiyse → prompt bitiminde devir teklifi.
- K6 (ölçüm): her kullanıcı prompt sonunda `introspection.read_session` → "Current context" → `metrics/context-snapshots.csv` satırı; deg bayrağı + oturum sonunda GERÇEK/SAHTE sınıflandırma (LOG).
- K7 (worker özetleme): 100+ satırlık okuma/özetleme → Worker B — **varsayılan = delege et, okuma** (FunkGoth, Oturum 18: CPU worker Mod A = sıfır geçiş maliyeti; yalnız decode hızı ~10 tok/s maliyet). Küçük dosyalarda YOK. Mod B (GPU) yalnız unattended batch.
- K3 (checkpoint kalitesi): Çalışma Döngüsü / Kaydet satırı.
- Tasarım + terminasyon kriterleri: `metrics/context-benchmark.md`.

## Sabit Kurallar (değiştirilemez)
1. Yalnızca E:\Bionic\ içinde yaz/sil. (2026-10-02: kök migrasyonu; E:\ diski diğer harness'lara da ayrılır)
2. Kullanıcı onayı olmadan silme / geri alınamaz işlem yok.
3. E:\Bionic\agent\ git yedeği güncel tutulur — her önemli adımdan sonra commit.
4. Projenin Docs/ dosyaları kullanıcı malıdır: agent önerir, kullanıcı onaylar.
   Kullanıcı onayı olmadan hiçbir içerik "canon" ilan edilemez.

## Çoklu Proje İskeleti (2026-10-01)
- E:\Bionic\ = agent'ın tahsisli çalışma alanıdır (çok proje + çok agent uygulaması + çok model için); E:\ diski diğer harness'lara da ayrılır (Ollama, Claude Code — 2026-10-02 FunkGoth kararı).
- `E:\Bionic\README.md` = evrensel giriş noktası: kimlik, disk haritası, sabit kurallar, yeni proje iskeleti.
- Global system prompt'lar **proje-bağımsız** tutulur: boş ya da tek satır — "Workspace: E:\Bionic\ — E:\Bionic\README.md'yi oku ve uygula."
- Yeni proje = `E:\Bionic\<proje-adı>\` kökü, standart iskelet: `README.md` (proje özeti) + `Docs\` (kullanıcı belgeleri) + `.agents\skills\` (proje skill'leri).
- Proje-özel talimatlar **her zaman projenin kendi klasöründe** yaşar; global prompt'a veya agent hafızasına taşınmaz.
- Agent hafızası (`E:\Bionic\agent\`) proje-agnostiktir: çalışma sistemi + proje-arası durum; proje içerikleri içermez.
- Taşıma/yeniden yapılandırma: yalnızca `move` (silme yok); tüm referanslar (skill, doc, uygulama kökü) güncellenmeden taşınmaz; LOG'a gerekçeli kayıt.

## Protokol Değişiklikleri
> Geçmiş: LOG.md ("Protokol" ara) — her değişiklik gerekçesiyle LOG'da kayıtlıdır (Sabit Kural). Bu bölüm yalnız yapısal özet.
- 2026-10-01 (O4): Çalışma Döngüsü 5 kural + STATE 4 bölüm + Devir "beklenen karar" · (O4) Çoklu Proje İskeleti + README giriş noktası · (O4) Delegation + VRAM kuralı + brief/result + runtime A/B/C · (O5) worker8b CPU-only kurulumu · (O5) Sistem Değerlendirmesi + otonom sub-agent yetkisi · (O5) light/full örnekleme + capture_overhead_s · (O6) `think:false` varsayılanı (~11x maliyet kanıtı)
- 2026-10-02 (O16): Mod B kuralı (önce unload; reload öncesi daima evict — 317.7s vs 17.44s kanıtı) · (O18) K1–K7 + context-benchmark.md (süreli deney →10-09) · (O8) Kök migrasyonu → `E:\Bionic\` · (O18) LOG=arşiv kuralı + Protokol Değişiklikleri'nin budanması (bu satır)

## Proje Haritası (EmberTale)
- Proje kökü: E:\Bionic\Project EmberTale\
- Kullanıcı belgeleri (taslak/canon statüsü onaya bağlı): Docs\ (VIZYON.md, ARCHITECTURE.md, DECISIONS.md, ROADMAP.md, Claude EmberTale md.zip)
- Proje skill'leri (her oturumda aktif): .agents\skills\
- Agent hafızası: E:\Bionic\agent\ (bu klasör)
- Agent geçici alanı: E:\Bionic\agent\scratch\
