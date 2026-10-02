# Handbook — İhtiyaç Hâlinde Referans (E:\Bionic\agent\handbook.md)
Boot'ta OKUNMAZ; PROTOCOL'den tam yollarla işaretli. Son güncelleme: 2026-10-02 (nihai karar oturumu, O20).

## Delegation Detay
### Runtime
| Runtime | Spesifikasyon | Kullanım |
|---|---|---|
| A | qwen3.8-27B, Bionic API `http://localhost:1234/api/v0/chat/completions` (`/api/v1` 404; JSON `Content-Type` zorunlu — 415 dersi) | yalnız FunkGoth GO'su (ana model context/VRAM tüketir) |
| B **varsayılan** | worker8b (qwen3:8b, 5.2 GB), Ollama port 11434 `http://localhost:11434/api/chat`; `model: worker8b`, `stream: false`, **`think: false`** (kanıt: 41s→3.7s), `num_gpu 0` (CPU), `num_ctx 8192`, `temperature 0.3` | 100+ satır özet/çıkarım/taslak, kaba tarama |
| C | yeni Bionic sohbet (tam agent, araçlı) | FunkGoth açar; brief'i ana agent yazar |

### Worker B çağrısı
`curl -s -d @payload.json http://localhost:11434/api/chat` (payload ayrı dosya — bash `$_` kırar; PowerShell `-File` + script, `ExecutionPolicy Bypass` çağrı bazlı).
Worker'ın aracı YOK → gerekli dosya parçaları brief'e gömülür. Brief zorunluları: amaç + kapsam + parçalar + çıktı formatı/kelime limiti + "çözülmüş/kapalı kararlar" + "reddedilen maddeyi yeniden önerme".
Doğrulama: ana agent her result'ı doğrular (kanıt: run 02'de 2 sapma + 1 cross-ref kaçağı ana agent yakaladı).

### Hız (ölçülmüş — runs.csv + baseline.md)
- CPU (Mod A): prefill ~63 tok/s; eval **10.2–10.4 tok/s** (DDR5 tavanı; thread/tuning anlamlı değil); 200 kelime ≈ 45–60s; ana modele etki **none** (VRAM sabit — koexist kanıtlı).
- GPU solo (Mod B): warm **101.9 / 103.2 / 108.1 tok/s** (~10x CPU); 18.5 tok/s = paralel kuyruk artifact'i (cold decode DEĞİL).
- 27B ana: ~17 tok/s ilk örnek (10-02, 32 tok API) — Ö3: her değerlendirmede yeniden örnekle → baseline.md bant.

### Mod B prosedürü (nihai 10-02)
1. Worker evict: Ollama `/api/ps` → `keep_alive:0` (size_vram → 0).
2. `lms unload` (VRAM 23xxx → ~1000 MiB).
3. 2×worker8b GPU (size_vram 6.3 GB'ar; 2×64K slot aynı anda sığmaz — 10-01 donması kanıtı).
4. 27B JIT reload: **VRAM boşken 17.44s** (runs.csv 06) vs 317.7s çakışma (kuralın kanıtı).
- Break-even ≈**200–250 output tok/batch** (~17–20s geçiş maliyeti).
- Yanlış Bionic endpoint bile JIT load'u tetikler; doğru endpoint `/api/v0/…`.

## Sistem Değerlendirmesi Detay
- İçerik: worker hız/kalite trendi · ana model durumu (API örneği) · GPU/RAM etkileşimi · havuz kalitesi · ≤3 öneri → FunkGoth; onay → DECISIONS.
- Aksiyon eşikleri: eval <4 tok/s → ara; "failed" ×2 aynı iş tipi → o iş tipini B'den çıkar; VRAM delta >256 MB → num_gpu 0 doğrula.
- Örnekleme (nihai 10-02): yeni iş tipi ilk + ~1/3 rastgele + anomali sonrası mutlaka; **run <30s → light varsayılan** (kanıt: run 04 capture_overhead %14 > %10 eşiği → mekanizma öneri üretti, Ö1 onaylandı). 10-02→10-09 ~%50 override **düşürüldü** (hedef ≥3, 1. günde 5 full ile doldu).
- runs.csv: append-only, 31 kolon (light'ta N/A boş); şema genişlerse eski düzenle satır yazma (ders: 10-01 şema hatası).
- Rapor: `metrics/eval-YYYY-MM-DD.md`.

## Context Ekonomisi Arşivi (K1–K9)
- Tasarım + terminasyon kriterleri: `metrics/context-benchmark.md` (arşiv; 10-02'de erken kapandı).
- Veri: `context-snapshots.csv` (K6) + `context-ledger.csv` (K9) + runs.csv.
- Ana veri noktaları: boot 19–21.5k/oturum · O18 tepe 59k → compaction −44.4k (FunkGoth UI) · 2 GERÇEK deg=1 (O18 veri yanlış yorumu, O19 boot hatası) · O18 K2-batch ihlali ~600 tok (defter yakaladı) · O20 boot 19k = en düşük.
- Nihai kararlar: PROTOCOL/Context Ekonomisi + DECISIONS (2026-10-02 O20 satırı) + `work/2026-10-02-nihai-karar-oturumu/rapor.md`.
- 10-09 = gözden geçirme noktası (kriterlerle yeniden kontrol; yeni karar değil).

## Bionic / LM Studio Gerçekleri (derslerden distille)
- Bionic JIT = on-demand **LOAD** (boşaltılan model, istek gelince yüklenir); klasik LM Studio Idle TTL/Auto-Evict'i Bionic yüklemeli modellerde YOK (#2423 açık). 27B'yi Bionic yükler (port 1234).
- Manuel yüklenen modeller Auto-Evict/Idle TTL'den muaftır (#2051 "stay forever") → asıl yaşam-döngüsü sahibi Bionic.
- nvidia-smi bu sürümde: `gpu_util` geçersiz → yalnız `memory.used`; "kullanım %" ≠ "bellek MB" — iddia sayıyla doğrulanır.
- Ollama: lokal GGUF import'u kırık → registry `pull` + `create`; `think:false` API parametresi.
- PowerShell: `$değişkenler` bash'te yutulur → `-File` + script dosyası; telemetri birimleri: KB/MB/bayt karışımı (semantik doğrula).
