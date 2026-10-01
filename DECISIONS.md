# DECISIONS — Çalışma Alanı Karar Günlüğü
Sıralama: en yeni üstte.

| Tarih | Karar | Gerekçe | Karar Veren |
|---|---|---|---|
| 2026-10-01 | Genişletilmiş telemetri: light (her çalıştırmada, sıfır maliyet) + full örneklerde VRAM dedicated+shared, RAM free/avail/commit, pagefile, GPU util (capture.ps1); örnekleme: yeni iş tipi ilk + ~1/3 rastgele + anomali sonrası mutlaka; ölçüm maliyeti (capture_overhead_s) izlenir, >%10 ise full oranı düşürülür | FunkGoth: token hızı + VRAM tampon/shared + RAM dalgalanması + paging takibi istedi; ölçüm maliyeti yüksekse raporlansın; random + farklı iş ağırlıklarında önerdi | Kullanıcı (FunkGoth) |
| 2026-10-01 | Otonom sub-agent yetkisi: ana agent worker B'yi verimli işlerde çağrı başı onaysız tetikler (C runtime hâlâ GO ister); her çalıştırma metrics/runs.csv'ye kaydedilir (kaynak, süre, ana modele etki, kalite); 24s'de 1 Sistem Değerlendirmesi — FunkGoth onayıyla tetik, 5 bölüm + ≤3 öneri | FunkGoth yetki verdi (B testini beğendikten sonra): verimli zamanlarda alt agent + günlük veri tabanlı öz-değerlendirme | Kullanıcı (FunkGoth) |
| 2026-10-01 | Worker runtime B somutlandı: `worker8b` (qwen3:8b) — Ollama port 11434, CPU-only (num_gpu 0), ctx 8192, temp 0.3 | FunkGoth B'yi onayladı (sıfır VRAM; ana 27B GPU'da); lokal GGUF import'u kırık → registry pull + create yolu (lessons.md) | Kullanıcı (FunkGoth) + agent (uygulama) |
| 2026-10-01 | Claude'un çalışma sistemi (DoD, Katman, Workflow) proje skill'i olarak miras alındı: .agents\skills\embertale-methodology\ | Kullanıcı talimatı; metodoloji skill'e gömülür, context şişmez (kullanıcının "A seçeneği") | Kullanıcı (FunkGoth) |
| 2026-10-01 | Qwen'in 4 dokümanı (VIZYON/ARCHITECTURE/DECISIONS/ROADMAP) TASLAK/ham girdi sayılır, canon değil | Önceki oturum analizi: sahiplik belirsiz, katman karışık, bazı kararlar tek taraflı | Önceki oturum (analiz), kullanıcıya iletildi |
| 2026-10-01 | L2 teknik içerik (struct/tag/Blueprint) bu oturumda işlenmez → L2 sohbetine yönlendirildi | Katman ayrımı: L1 vizyon ≠ L2 iskelet | L0 metodoloji |
| 2026-10-01 | L1 vizyon skill'i henüz oluşturulmadı; kullanıcı onayı sonrası kurulacak | Onaysız içerik canon ilan edilemez (Sabit Kural 4) | L0 metodoloji |
