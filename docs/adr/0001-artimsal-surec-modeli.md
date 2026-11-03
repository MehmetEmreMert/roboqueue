# ADR-0001: Artırımsal Süreç Modelinin Seçilmesi

- **Durum:** Kabul edildi
- **Tarih:** 2026-11-03

## Bağlam

RoboQueue, iki farklı mimari alternatifin uygulanıp karşılaştırılmasını gerektiriyor. Ekip üç kişiden oluşuyor ve başka derslerin projeleriyle paralel çalışıyor, bu yüzden her hafta ayrılabilecek süre sınırlı ve dalgalı. Gereksinimlerin büyük kısmı baştan biliniyor, ancak çarpışma önleme, arıza yönetimi ve olay güdümlü iletişim gibi bölümlerin gerçek maliyeti uygulanmadan tahmin edilemiyor.

## Karar

Geliştirmede **artırımsal (incremental) süreç modeli** kullanılacak. Sistem, her biri çalışan bir sürüm üreten fazlara bölünecek:

1. Tek robotlu çekirdek sistem (MVP, Alternatif A)
2. Çoklu robot, öncelik ve çarpışma önleme
3. Heartbeat, lease ve yeniden deneme mekanizması
4. Alternatif B (olay güdümlü mimari)
5. Deneyler ve rapor

## Gerekçe

- Her artım sonunda çalışan bir sürüm olur. Zaman daralırsa bir önceki artım teslim edilebilir.
- En önemli işlev (görev atama ve rota) ilk artımda test edilir, riskli kısımlar erken ortaya çıkar.
- Alternatif B, A'nın ortak bileşenlerini (robot ajanı, veritabanı, panel) yeniden kullanarak eklenebilir.
- Dersin izlencesindeki "farklı mimari alternatiflerini uygulayarak karşılaştırma" hedefi artımlı bir yapıyla doğal olarak örtüşür.

## Sonuçlar

- Her fazın sonunda sürüm etiketi atılacak ve demo yapılabilecek durumda olacak.
- Artımları tanımlamak için baştan genel bir mimari çerçeve gerekiyor (bu çerçeve Faz 1'de C4 ve UML ile çıkarılacak).
- Artımlar arasında yeniden düzenleme (refactoring) yapmak gerekebilir ve bunun için zaman ayrılmalı.

## Reddedilen Alternatifler

- **Çağlayan (Waterfall):** Gereksinimler büyük oranda belli olsa da, test aşamasının en sona kalması ve geri dönüşün maliyetli olması bu proje için riskli.
- **Helezonik (Spiral):** Risk analizi odaklı ve çok sayıda ara belge gerektiriyor, küçük ve düşük riskli bir ders projesi için fazla ağır.
- **Scrum:** Düzenli sprint toplantıları ve roller için ekibin zamanı yetersiz. Artırımlı çalışmanın hafif toplantı ritmi (haftada bir kısa görüşme) yeterli.
