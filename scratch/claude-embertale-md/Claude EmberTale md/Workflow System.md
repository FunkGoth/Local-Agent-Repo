Workflow System

Project

Unreal Engine Project EmberTale

Last updated

Sep 28, 2026

Summary

EmberTale çalışma sistemi — Proje Hafızası/Sohbet/Sohbet Özeti/Project Knowledge terimleri, çalışma akışı, Sohbet Özeti üretim kuralı. Katman yapısı → layer-structure.md, şablonlar → templates.md, DoD → dod.md.



Details

Terminoloji ve Sistem:



"Proje Hafızası": Claude'un bu proje için otomatik, arka planda tuttuğu, her sohbette önceden yüklü gelen, sınırlı boyutlu yapılandırılmış not sistemi. Sadece Claude tarafından, kullanıcı onayıyla yazılır/silinir — kullanıcının kesin denetimi altındadır.

"Sohbet": Bu projede, belirli bir sistem veya konu üzerinde çalışılan, veri işleme amaçlı kullanılan çalışma alanı.

"Sohbet Özeti": Bir sohbet sonunda/geçişinde Claude'un ürettiği, o sohbette yapılan işin/kararların/kodun özetlendiği çıktı. Kullanıcı bunu Project Knowledge'a, sohbetle AYNI isimle kaydeder. Standart şablonu için: templates.md.

"Project Knowledge" (mobil uygulamada bu isimle görünür) / "Context" (masaüstü uygulamada bu isimle görünür): Aynı özellik, iki farklı arayüzde farklı isimlendirilmiş. Kullanıcının kendi yönettiği, dosya yükleyip sakladığı Anthropic Projects özelliği. Sohbet Özetlerinin kalıcı arşivlendiği yer — asıl kaynak (source of truth) burasıdır, Proje Hafızası değil.

Çalışma Akışı:



Her sistem/konu kendi sohbetinde çalışılır (sohbet adı = sistem adı).

Bir sohbetten yeni bir sohbete geçilirken, Claude o sohbetin Sohbet Özetini üretir.

Kullanıcı bu özeti Project Knowledge'a, sohbetle aynı isimli dosya olarak yükler.

Yeni bir sohbette önceki bir sisteme referans gerektiğinde, Claude ilgili isimdeki Sohbet Özeti dosyasını Project Knowledge içinden okur.

Proje Hafızası yalnızca bu işleyişin kurallarını ve nadiren değişen proje-geneli kararları tutar — sistem detayları/kod burada TUTULMAZ, Project Knowledge'da kalır.

Sohbet Özeti Üretim Kuralı:



Varsayılan tetikleyici komut: "Sohbet Özeti ver" — Claude bu komutla, mevcut sohbetin tamamını templates.md'deki standart şablona göre özetleyip indirilebilir bir .md dosyası olarak üretir. Dosya adı = sohbet adı (kullanıcı Project Knowledge'a bu isimle yükler).

Parametreli kullanım: Kullanıcı komutla birlikte (a) dosya adını ve (b) kapsamı (sohbetin tamamı yerine belirli bir bölüm/konu) belirtebilir. Örnek: "Sohbet Özeti ver, ismi: L1 Proje İskeleti, sadece proje iskeleti konuşmasını içersin." Kapsam belirtilmezse varsayılan olarak sohbetin tamamı özetlenir.

Kural: Sohbet Özeti dosyaları, Proje Hafızasında zaten tutulan terminoloji/workflow tanımlarını (Proje Hafızası, Sohbet, Sohbet Özeti, Project Knowledge tanımları gibi) TEKRARLAMAZ — sadece o sohbete özgü içerik (kararlar, kod, yapı, durum) yer alır.

