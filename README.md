# RoboQueue

Dağıtık görev kuyruğu tabanlı depo robot filosu simülatörü.

**Ders:** YMH301 Yazılım Mimarisi, Ankara Üniversitesi Yazılım Mühendisliği Bölümü (2026-2027 Güz)

## Proje Özeti

Operatör, 15×15 ızgara şeklindeki bir depo haritasında "A3 rafındaki ürünü paketleme noktasına getir" gibi görevler gönderir. Görevler öncelikli bir kuyruğa girer, boştaki uygun robota atanır, robot A* algoritmasıyla rotasını bulup simülasyonda ilerler. Robot arızalanırsa görev kuyruğa geri döner ve başka bir robota atanır.

Projenin asıl amacı uygulamanın kendisi değil, **aynı işlevi iki farklı yazılım mimarisiyle gerçekleştirip karşılaştırmaktır.**

## Mimari Alternatifler

| | Alternatif A: Merkezi İstemci-Sunucu | Alternatif B: Olay Güdümlü |
|---|---|---|
| İletişim | Robotlar REST ile sunucuya sorar (polling) | Servisler Redis Streams üzerinden olay yayınlar ve dinler |
| Görev dağıtımı | FastAPI içindeki zamanlayıcı | Ayrı Scheduler servisi |
| Güçlü yön | Basit, hata ayıklaması kolay | Gevşek bağlı, genişletilebilir |
| Zayıf yön | Polling gecikmesi, tek nokta bağımlılığı | Kurulum ve olay sırası karmaşıklığı |

Her iki alternatifte de C++ robot ajanı, rota algoritmaları, MySQL şeması ve Streamlit paneli ortaktır.

## Temel Özellikler

- Görev oluşturma, takip ve öncelikli kuyruk
- Birden fazla robot (3 temel, performans testinde 10 ve 20)
- A* ve Dijkstra rota algoritmaları (Strategy Pattern)
- Robot ve görev için ayrı durum makineleri (State Pattern)
- Heartbeat + lease + `assignment_version` ile arıza toleransı ve yeniden atama
- Hücre rezervasyonuyla çarpışma önleme
- İşlem geçmişinin MySQL'e kaydı

## Teknoloji

| Katman | Teknoloji |
|---|---|
| Robot ajanı, rota planlama | C++ (CMake) |
| Sunucu ve servisler | Python, FastAPI |
| Veritabanı | MySQL |
| Olay altyapısı (Alternatif B) | Redis Streams |
| Panel | Streamlit |
| Çalıştırma | Docker Compose |

## Klasör Yapısı

```
roboqueue/
├── docs/
│   ├── adr/             Mimari karar kayıtları
│   ├── architecture/    C4 ve UML diyagramları
│   └── reports/         Öneri ve final raporu
├── robot-agent/         C++ robot ajanı
├── server/              FastAPI sunucu (Alternatif A)
├── event-services/      Scheduler ve Event Logger (Alternatif B)
├── dashboard/           Streamlit paneli
├── deploy/              Docker Compose dosyaları
└── experiments/         Ölçüm senaryoları ve sonuçlar
```

## Süreç Modeli

Artırımsal (incremental) geliştirme. Gerekçe için bkz. [ADR-0001](docs/adr/0001-artimsal-surec-modeli.md).

## Yol Haritası

| Faz | Hedef |
|---|---|
| 0 | Altyapı ve proje önerisi |
| 1 | Gereksinim, UML, C4 ve durum makinesi tasarımı |
| 2 | MVP: tek robot, A*, FastAPI, MySQL, panel (Alternatif A) |
| 3 | Çoklu robot, öncelik, çarpışma önleme, heartbeat + lease |
| 4 | Alternatif B: Redis Streams, Scheduler, Event Logger |
| 5 | Karşılaştırmalı deneyler |
| 6 | Final raporu ve sunum |

## Karşılaştırma Metrikleri

Atama gecikmesi (ortalama ve P95), throughput, arıza sonrası kurtarma süresi, kaybolan ve çift atanan görev sayısı, değiştirilebilirlik (önceden belirlenmiş değişikliklerin etkilediği dosya sayısı).

## Çalışma Kuralları

- `main` dalına doğrudan push yapılmaz, herkes `feature/<konu>` dalında çalışıp pull request açar.
- Önemli mimari kararlar `docs/adr/` altında kayıt altına alınır.
- Her faz sonunda çalışan bir sürüm etiketlenir (`v0.1`, `v0.2`, ...).

## Ekip

3 kişilik ekip. Rol dağılımı `docs/reports/` altındaki öneri raporunda yer alır.

## Durum

Faz 0: Altyapı kuruluyor.
