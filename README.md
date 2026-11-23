# Yakın — Android mesajlaşma MVP'si

Durum/hikâye, herkese açık akış ve reklam içermeyen; kişi isteği kabul edildikten sonra bire bir metin sohbeti açan Expo Android uygulaması.

## Şu an neler var?

- E-posta OTP ile giriş/kayıt ekranı
- Gmail/e-posta ile tek tek kişi arama
- Bekleyen kişi isteği; kabul edilince sohbet oluşturulması
- Gerçek zamanlı metin mesajları ve mesaj zamanı
- Engelleme, şikâyet, arama sınırı ve Supabase RLS politikaları
- Okundu bilgisi, durum, takip, herkese açık paylaşım yok

Bu kaynak kodu uygulama ve veritabanı yapısını hazırlar. Henüz bir Supabase projesine bağlanmadı; `.env.example` içindeki değerler örnektir ve uygulama gerçek hesap/mesaj oluşturamaz.

## Geliştirme ortamı

Node.js LTS ve Android telefonunda Expo Go gerekir. Android SDK bu makinede kurulu değil; telefon üzerinden önizleme yapılabilir. Geliştirme bilgisayarı ve telefon aynı ağa bağlandıktan sonra:

```powershell
npm start
```

QR kodu Expo Go ile okut. Gerekirse `npx expo start --tunnel` kullan.

## Gerçek hesap ve sohbet servisini bağlama

1. Kendi Supabase hesabında bir proje oluştur.
2. Supabase SQL Editor'da `supabase/migrations/202610150001_initial_schema.sql` dosyasını bir kez çalıştır.
3. Supabase **Authentication → URL Configuration → Redirect URLs** alanına `yakinmesaj://auth/callback` ekle. Böylece e-postadaki doğrulama bağlantısı `localhost` yerine uygulamayı açar. E-posta şablonun `{{ .ConfirmationURL }}` kullanmalı. Kodla giriş için şablonda `{{ .Token }}` da gösterilebilir.
4. E-postaların proje ekibin dışındaki adreslere gönderilebilmesi için Supabase Auth'a özel SMTP sağlayıcısı bağla. Bu sürüm SMS ile kayıt veya giriş kullanmaz.
5. `.env.example` dosyasını `.env` olarak kopyalayıp kendi proje URL'ni ve publishable key değerini yaz. `service_role` anahtarını uygulamaya koyma.
6. `npm start` ile yeniden başlat ve iki farklı cihaz/hesapla akışları dene.

Kişi araması doğrulanmış tam e-posta eşleşmesiyle çalışır; aramalar kullanıcı başına saatte 30 ile sınırlıdır. İki kullanıcı da kişi isteğini kabul etmeden sohbet açılamaz. RLS doğrudan veritabanı erişimini sınırlar.

## Yayına çıkmadan önce

- Mesajlar şu an uçtan uca şifreli değildir. Mesaj gövdesi sunucu veritabanında saklanır; bu uygulama gizli/şifreli mesajlaşma ürünü olarak tanıtılmamalıdır.
- Şikâyet kayıtlarını inceleyen bir moderasyon süreci ve çocuk güvenliği için olay müdahale akışı henüz yoktur.
- Türkiye'de 15 yaş altına yönelik sosyal ağ düzenlemesinin bağımsız mesajlaşma hizmetine uygulanıp uygulanmadığı netleştirilmeden çocuklara açık şekilde yayınlama. Kimlik/yaş doğrulaması eklemek gerekiyorsa yalnızca hukuka uygun ve veri minimizasyonu yapan bir doğrulama sağlayıcısı kullan.
- Gizlilik bildirimi, veri saklama/silme, yedekleme, kötüye kullanım önlemleri ve mağaza gereklilikleri yayın öncesi tamamlanmalıdır.

## Teknoloji

Expo SDK 58 / React Native, Expo Router ve Supabase Auth, Postgres, RLS, Realtime.
