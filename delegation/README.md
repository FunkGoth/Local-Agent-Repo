# Delegation — worker brief & sonuçları

- `brief-<konu>.md` — görev brief'i: amaç, kapsam, gerekli dosya parçaları, çıktı formatı + kelime limiti.
- `result-<konu>.md` — worker'ın KOMPAKT çıktısı (≤200 kelime veya sabit format).
- VRAM kuralı: aynı anda TEK worker (24 GB tavan; 2026-10-01 donması: 2×64K slot sığmıyor).
- Runtime'lar: A = localhost:1234 (aynı model, sınırda) · B = CPU küçük modeli, 2. port, GPU katman 0 (önerilen) · C = yeni Bionic sohbet (tam agent; FunkGoth açar, brief'i ana agent yazar).
- Ana agentın context'i yalnız brief+result kadar büyür; hacimli okuma worker'ın atılabilir havuzunda kalır.
