# ADR-0002: Olay Altyapısı Olarak Redis Streams

- **Durum:** Kabul edildi (Alternatif B için)
- **Tarih:** 2026-11-03

## Bağlam

Alternatif B'de görev oluşturma, atama ve robot komutları servisler arasında olaylarla iletilecek. Görev kuyruğu sisteminde bir olayın kaybolması, görevin hiç işlenmemesi anlamına gelir. Özellikle Scheduler servisi geçici olarak kapalıyken yayınlanan `task.created` olayının, servis geri döndüğünde işlenebilmesi gerekiyor.

## Karar

Olay altyapısı olarak **Redis Streams** ve **consumer group** yapısı kullanılacak. Scheduler olayı işledikten sonra ACK gönderecek. İşlenmemiş olaylar `XPENDING` ile izlenecek.

Görevin asıl kaynağı MySQL olarak kalacak. Sunucu veya Scheduler yeniden başladığında, MySQL'deki bekleyen (`QUEUED`, `RETRY_WAIT`) görevler kontrol edilecek.

## Gerekçe

- Mesajlar kalıcıdır, tüketici kapalıyken gelen olaylar sonradan okunabilir.
- Consumer group ve ACK mekanizması "en az bir kez işleme" garantisi sağlar.
- Redis tek bir konteynerle çalıştırılabilir, Docker Compose ile kurulumu basittir.
- RabbitMQ'ya göre daha az yapılandırma gerektirir, ders projesi kapsamına daha uygundur.

## Sonuçlar

- "En az bir kez" teslimat nedeniyle olaylar tekrar gelebilir. Görev atama işlemi bu yüzden **idempotent** olmalı ve `assignment_version` kontrolüyle korunmalı.
- Pub/Sub'a göre biraz daha fazla geliştirme gerekir (consumer group yönetimi, ACK, bekleyen mesaj kontrolü).
- Redis'in kendisi tek hata noktası olabilir. Bu risk, raporun karşılaştırma bölümünde Alternatif A ile birlikte değerlendirilecek.

## Reddedilen Alternatifler

- **Redis Pub/Sub:** Mesajlar kalıcı değil. Tüketici kapalıyken yayınlanan olay kaybolur, görev kuyruğu için kabul edilemez.
- **RabbitMQ:** Kuyruk, ACK ve yeniden deneme özellikleri güçlü, ancak ek altyapı ve yapılandırma yükü bu projenin kapsamına göre fazla.
- **MQTT:** Robotlarla haberleşme için uygun, fakat iş kuyruğu garantilerinin (ACK, yeniden işleme) ayrıca tasarlanması gerekiyor.
- **Transactional Outbox:** MySQL ve Redis arasındaki tutarsızlığı çözerdi, fakat proje kapsamını gereksiz büyütüyor. Yerine açılışta bekleyen görev taraması kullanılacak.
