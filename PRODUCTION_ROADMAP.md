# Detaycım Production Roadmap

Bu proje statik frontend + demo business logic prototipidir. Gerçek para, stok, sipariş, müşteri veya dış servis başarısı üretmez.

## Hedef mimari

```text
Frontend
  -> Secure Backend/API
  -> CatalogRepository / OrderRepository / CustomerRepository
  -> PaymentProvider / AccountingProvider / ShippingProvider / WhatsAppProvider
  -> Database, queue, audit log ve monitoring
```

`architecture/providers.js` gerçek ağ çağrısı yapmayan adapter sınırlarını tanımlar. Production adapter'ları yalnızca backend ortamında, secret manager ve authenticated API üzerinden uygulanmalıdır.

## Gerçek admin paneli

Demo Store Manager localStorage ile çalışır ve ekranda gerçek sunucuya kaydetmediğini belirtir. Production admin için authenticated admin, roller/yetkiler, ürün CRUD, stok, fiyat, indirim, merchandising, sipariş, müşteri, kargo, muhasebe, WhatsApp ve audit log modülleri gerekir.

## Kimlik ve müşteri hesabı

Frontend'deki Hesabım modalı yalnızca demo UI'dır; parola saklamaz, gerçek giriş/üyelik, sipariş geçmişi, adres veya müşteri kaydı oluşturmaz. Production için browser -> Secure Auth API -> backend -> database akışı, session/token güvenliği, password hashing, reset akışı, rate limiting, RBAC ve audit log gerekir.

## Fiyat ve sipariş güvenliği

Frontend fiyatı, indirim oranı, stok ve toplamı güvenilir kabul edilmemelidir. Backend ürün fiyatını ve kampanyayı server saatine göre tekrar doğrulamalı; sepet toplamını yeniden hesaplamalı; stok, kargo, vergi ve ödeme isteğini server-side oluşturmalıdır.

## Kargo ve ürün verisi

Ürün modeline production'da `weightKg`, SKU, stok durumu, görsel URL'si, gerçek `addedAt` ve satış metriği eklenebilir. Gerçek kargo tarifesi olmadan ücret hesaplanmaz; ShippingProvider yalnızca backend tarafında bağlanır.

## Ödeme, Bizim Hesap ve WhatsApp

Şu anda PaymentProvider, AccountingProvider, ShippingProvider ve WhatsAppProvider yalnızca yapı sınırıdır. Meta/WhatsApp tokenı, ödeme anahtarı, Bizim Hesap veya kargo credentials frontend'e konulmamalıdır. Gerekli hesap ve API erişimleri işletme sahibinden alınmadan entegrasyon etkinleştirilmez.

## Analytics

Frontend event abstraction `architecture/analytics.js` ile aynı event adlarını kullanacak şekilde tasarlanmıştır: `view_item`, `search`, `select_item`, `add_to_cart`, `remove_from_cart`, `view_cart`, `begin_checkout`, `whatsapp_support_click`, `wishlist_add`. Gerçek analytics sağlayıcısı sonradan backend/consent katmanı ile bağlanmalıdır.

## LocalStorage sınırı

Wishlist, recent ve demo manager state'i tarayıcıya bağlıdır; güvenli kullanıcı hesabı, stok veya sipariş kaynağı değildir. Tarayıcıdaki kullanıcı verisi değiştirilebilir ve hassas veri burada saklanmamalıdır.

## Hukuki içerik

Gizlilik, KVKK, mesafeli satış, teslimat, iade/değişim ve iletişim metinleri işletme tarafından sağlanıp hukuk değerlendirmesinden geçirilmelidir. Demo, eksik hukuki metinleri uydurmaz.
