# Detaycım Vitrin ve Kampanya Hazırlığı

Bu proje şu anda statik demo storefront'tur. Vitrin alanları ve kampanya veri modeli backend olmadan gerçek satış, popülerlik veya indirim iddiası üretmez.

## Veri modeli

Ürün kaydı aşağıdaki alanları destekler:

```js
{
  id,
  slug,
  brand,
  name,
  category,
  price,
  image,
  featured,
  addedAt,
  salesCount,
  discountPercent,
  discountActive,
  discountStart,
  discountEnd,
  campaign
}
```

Mevcut 59 ürün için işletme tarafından doğrulanmamış `featured`, `addedAt`, `salesCount` ve kampanya alanları boş/kapalıdır. Tarih veya satış sayısı uydurulmaz.

## Ana sayfa vitrinleri

- **Vitrin Ürünleri:** Yalnızca `featured: true` olan ürünleri gösterir. Şu an işletme seçimi olmadığı için bekleme durumu gösterilir.
- **Son Eklenenler:** Yalnızca gerçek `addedAt` tarihi olan ürünleri tarihe göre sıralar.
- **Popüler Gruplar:** Gerçek katalog kategorilerini listeler ve ürün kataloğuna filtreli geçiş yapar. Popülerlik/satış iddiası içermez.
- **Çok Satanlar:** Yalnızca gerçek `salesCount` metriği olan ürünleri sıralar.
- **İndirimdekiler:** Yalnızca tarih ve aktiflik kontrolünden geçen kampanyaları gösterir.

Vitrin kartları ana ürün detay modalına ve mevcut sepete bağlıdır.

## İndirim kuralları

Desteklenen hızlı oranlar: `%5`, `%10`, `%15`, `%20`, `%25`, `%50`, `%80`, `%100`. Hesaplama yuvarlanır ve kuruşlu değerler iki ondalıkla gösterilir:

```text
discountedPrice = round(price - price * discountPercent / 100, 2)
```

`discountStart` ve `discountEnd` tarihleri kampanyanın frontend'de gösterilmesi için kontrol edilir. Bu kontrol production fiyat güvenliği değildir.

`%100` indirim için yönetim panelinde ikinci onay, audit kaydı ve server-side doğrulama zorunlu olmalıdır. Kullanıcıya ücretsiz sipariş oluşturma kararı yalnızca backend tarafından verilmelidir.

## Production yönetim paneli planı

Mobil uyumlu panel aşağıdaki işlemleri backend API üzerinden yapmalıdır:

- Ürün ekleme/düzenleme
- Vitrin açma/kapatma
- Kampanya açma/kapatma
- İndirim oranı ve tarih aralığı
- Kategori, marka, fiyat ve stok
- Gerçek eklenme tarihi
- Satış metriği
- Ürün görseli URL'si
- Kampanya geçmişi ve audit kaydı

Kupon/kampanya motoru ileride şu kapsamları destekleyebilir:

- Kupon kodu
- Ürün, marka veya kategori indirimi
- Mağaza geneli indirim
- Minimum sepet tutarı
- Ücretsiz kargo
- Tarih aralığı
- Kampanya öncelik ve birleştirme kuralları

## Production güvenliği

Frontend'deki fiyat, indirim, stok ve sepet toplamı güvenilir kabul edilmemelidir. Gerçek sipariş sırasında backend:

1. Ürün ve fiyatı veri kaynağından tekrar okur.
2. Kampanya tarihini server saatine göre doğrular.
3. İndirim yetkisini ve kullanım limitini kontrol eder.
4. `%100` indirimi ikinci onay/audit kuralıyla doğrular.
5. Stok, kargo, vergi ve sipariş toplamını yeniden hesaplar.
6. Ödeme sağlayıcısına yalnızca server tarafından doğrulanmış toplamı gönderir.

Tarayıcıda JavaScript değiştirilerek fiyat veya kampanya manipüle edilmesi production siparişini etkileyememelidir.

## Gerçek veriler geldiğinde

İşletme sahibi doğrulanmış vitrin ürünlerini, ürün eklenme tarihlerini, satış metriği politikasını, kampanya oranlarını/tarihlerini ve gerçek görsel URL'lerini sağladığında alanlar backend katalog kaynağından doldurulmalıdır. Bu statik demo dosyasına rastgele satış, tarih, kampanya veya görsel eklenmemelidir.
