# Detaycım V2 Premium Demo

GitHub Pages uyumlu, bağımsız HTML/CSS/JS storefront prototipi.

## Demo kapsamı
- Mobil öncelikli responsive premium storefront
- 59 ürünlük doğrulanmış katalog verisi ve görsel placeholder sistemi
- Ürün/marka arama, kategori, marka ve fiyat filtreleri
- Fiyat ve ürün adına göre sıralama
- localStorage destekli demo sepeti ve sepetten ürün kaldırma
- WhatsApp üzerinden demo sipariş/destek mesajı oluşturma
- Instagram, Trendyol, Hepsiburada ve WhatsApp dış kanal bağlantıları
- Boş sonuç durumu, klavye Escape desteği ve erişilebilir drawer işaretleri
- SEO temel meta bilgileri ve entegrasyon hazırlık paneli
- Ürün detay modalı, ürün slug hazırlığı ve opsiyonel görsel alanı
- Veri yokken satış/indirim iddiası üretmeyen vitrin alanları
- Tarihli indirim ve server-side fiyat doğrulamasına hazır veri modeli
- Wishlist, son görüntülenenler, deterministic öneriler ve gelişmiş mağaza filtreleri
- Açıkça demo olarak işaretlenmiş Store Manager ve localStorage merchandising state'i

## Gerçek entegrasyon sınırı
Ödeme, Bizim Hesap, kg bazlı kargo, stok/sipariş yönetimi ve WhatsApp otomasyonu canlı değildir.
WhatsApp bağlantısı yalnızca işletmenin mevcut numarasına destek mesajı açar; gerçek sipariş oluşturmaz.
Canlı entegrasyonlar müşteri onayı ve gerekli hesap/API erişimleri sonrası yapılmalıdır.

## Vitrin ve kampanya hazırlığı
Vitrin, son eklenenler, kategori grupları, çok satanlar ve indirim alanları gerçek veri yokken güvenli bekleme durumları gösterir. Ürün veri modeli `featured`, `addedAt`, `salesCount`, `discountPercent`, `discountActive`, `discountStart` ve `discountEnd` alanlarını destekler.
Production yönetim paneli, kampanya kuralları ve server-side fiyat doğrulaması için [STOREFRONT_SETUP.md](STOREFRONT_SETUP.md) dosyasına bakın.

## Production mimarisi
Provider sınırları, analytics event katmanı, kargo `weightKg` hazırlığı, backend fiyat güvenliği ve gerçek admin yol haritası için [PRODUCTION_ROADMAP.md](PRODUCTION_ROADMAP.md) dosyasına bakın. `architecture/providers.js` ağ çağrısı yapmayan güvenli adapter sınırlarını, `architecture/analytics.js` izinli event isimlerini tanımlar.

## WhatsApp otomasyon hazırlığı
`automation/intent-router.js` ve `automation/webhook-handler.js` gerçek API çağrısı yapmayan, server-side entegrasyona hazırlanmış mock çekirdektir.
Intent, ürün eşleşmesi, güvenli fallback, insan handoff ve webhook challenge akışı için [WHATSAPP_SETUP.md](WHATSAPP_SETUP.md) dosyasına bakın.

Mock testleri çalıştırmak için:

```powershell
node --test automation/intent-router.test.js
```
