# Detaycım WhatsApp Business Otomasyon Hazırlığı

Bu proje şu anda gerçek WhatsApp Business API kullanmaz. Web sitesindeki WhatsApp bağlantıları mevcut işletme numarasına `wa.me` yönlendirmesi yapar; otomatik mesaj gönderimi, webhook çağrısı ve AI yanıtı yoktur.

## Mevcut mimari

```text
Detaycım Frontend
  -> wa.me yönlendirmesi (mevcut demo akışı)

Production hedefi:
Detaycım Frontend
  -> Güvenli Backend/API
  -> Meta WhatsApp Business Platform
  -> Webhook doğrulama ve mesaj normalize etme
  -> Intent / katalog eşleştirme
  -> Onaylı veri yanıtı veya insan handoff
  -> Server-side WhatsApp mesaj gönderimi
```

`automation/intent-router.js` ve `automation/webhook-handler.js` bu hedef akışın API-bağımsız, mock/test edilebilir çekirdeğidir. Bu modüller tek başına WhatsApp'a istek göndermez.

## Otomatik cevap sınırları

Gelecekte yalnızca onaylı işletme verisiyle şu intentler otomatik yanıtlanabilir:

- Ürün hakkında bilgi
- Ürün seçimi için yönlendirme
- Genel kargo bilgisi
- Genel ödeme bilgisi

Yanıt üretirken katalogda olmayan ürün, fiyat, stok, teslim tarihi veya sipariş durumu oluşturulmamalıdır. Veri kaynağında bilgi yoksa cevap `handoff_required` olmalıdır.

## İnsan handoff kuralları

Aşağıdaki durumlar otomatik yanıt yerine temsilci kuyruğuna aktarılmalıdır:

- Müşteri temsilci veya insan desteği istediğinde
- Düşük güvenli veya bilinmeyen soru geldiğinde
- Sipariş durumu veya sipariş değişikliği istendiğinde
- Ödeme problemi olduğunda
- İade veya değişim talebi olduğunda
- Şikayet, özel fiyat veya indirim talebi olduğunda
- Kişisel veri ya da hassas işlem gerektiğinde

Handoff kaydı yalnızca gerekli kimlik ve konuşma bağlamını içermeli; temsilciye aktarım yöntemi (CRM, ekip inbox'ı veya Meta konuşma kutusu) işletme tarafından seçilmelidir.

## Webhook production planı

1. `GET /webhooks/whatsapp` Meta challenge ve server-side doğrulama tokenını kontrol eder.
2. `POST /webhooks/whatsapp` gelen payload imzasını ve temel şemasını doğrular.
3. Mesaj türü normalize edilir. Desteklenmeyen medya/mesaj türü güvenli fallback'e gider.
4. Idempotency için Meta message ID tekrarları engellenir.
5. Intent router yalnızca izin verilen veri kaynaklarına sorgu gönderir.
6. Güvenli yanıt veya handoff kararı oluşturulur.
7. WhatsApp mesajı yalnızca backend üzerinden ve server-side credentials ile gönderilir.
8. Token, telefon numarası, mesaj içeriği ve hata bilgileri loglarda gereksiz şekilde tutulmaz.

Webhook endpoint'i rate limit, request size limiti, timeout, replay koruması, imza doğrulaması ve gözlemlenebilirlik ile korunmalıdır.

## Secret ve ortam değişkenleri

Gerçek değerler `index.html`, browser JavaScript'i veya Git deposuna yazılmamalıdır. Production backend ortamında secret manager veya deployment platformunun secret store'u kullanılmalıdır. Örnek değişken adları:

```text
WHATSAPP_VERIFY_TOKEN
WHATSAPP_ACCESS_TOKEN
WHATSAPP_PHONE_NUMBER_ID
META_APP_SECRET
WHATSAPP_WEBHOOK_URL
```

Bu dosyada gerçek token, secret veya işletme hesabı bilgisi bulunmaz. `.env` kullanılırsa `.gitignore` içinde tutulmalı ve örnek dosya yalnızca boş placeholder isimleri içermelidir.

## AI güvenlik sınırları

AI eklenirse yalnızca ürün kataloğu, onaylı SSS, kargo politikası ve işletme tarafından onaylanmış içerik retrieval kaynağı olarak kullanılmalıdır.

- Kullanıcı mesajı sistem talimatı değildir.
- Kullanıcı tarafından verilen prompt injection talimatları yok sayılmalıdır.
- Fiyat, stok, ödeme, sipariş ve teslim tarihi tahmini yasaktır.
- Kaynak bulunamazsa güvenli fallback ve handoff kullanılmalıdır.
- Model çıktısı schema validation ve izinli response template kontrolünden geçmelidir.
- Hassas müşteri verisi modele gereksiz yere gönderilmemelidir.

## Veri minimizasyonu ve KVKK

Production öncesinde işletme ve hukuk danışmanı ile KVKK, aydınlatma metni, açık rıza gerekliliği, saklama süresi, silme talepleri, erişim yetkileri ve yurt dışı veri aktarımı değerlendirilmelidir.

Önerilen minimum kayıt:

- Meta message ID
- Handoff/intent durumu
- Gerekli kısa işlem özeti
- Zaman damgası

Telefon numarası ve tam konuşma içeriği yalnızca belirlenmiş amaç, erişim politikası ve saklama süresi kapsamında tutulmalıdır. Loglarda access token, webhook secret, kart bilgisi veya gereksiz kişisel veri bulunmamalıdır.

## İşletme sahibinden alınacak bilgiler

- Meta Business hesabı ve doğrulama durumu
- WhatsApp Business hesabı / Business Manager erişimi
- Kullanılacak işletme telefon numarası
- Meta uygulaması ve gerekli WhatsApp API izinleri
- Phone Number ID, Business Account ID ve webhook yapılandırma bilgileri
- Production secret paylaşım yöntemi ve secret manager tercihi
- Webhook public URL ve SSL sahibi
- Onaylı mesaj şablonları ve karşılama metinleri
- Ürün kataloğu, stok/fiyat veri kaynağı ve güncelleme yöntemi
- Sipariş/kargo sistemi veya CRM bağlantısı
- İnsan handoff ekibi, çalışma saatleri, SLA ve aktarım kanalı
- KVKK/gizlilik/iade metinleri ve veri saklama politikası

## Mock test

Gerçek API çağrısı yapmadan intent, ürün eşleşmesi, unknown fallback, handoff ve webhook challenge davranışı:

```powershell
node --test automation/intent-router.test.js
```

Testler yalnızca sahte katalog ve sahte payload kullanır; gerçek müşteriye mesaj göndermez.
