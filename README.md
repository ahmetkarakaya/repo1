# Silent PN Generator

## Uygulamanın Temel Özellikleri:

Bu uygulama tanımlanabilir bir süre boyunca BiP uygulamasını kullanmayan kullanıcılara gönderilmek üzere **Silent PN** hazırlar ve bu isteği **PNSender** uygulamasına gönderir.
BiP uygulamasını silmiş kullanıcılar **frozen** durumuna gelir ve bu sayede rapor ve ölçmlerde daha isabetli tahminler yapılailir.

**Uygulama Parametreleri.**

Parametre Adı | Örnek Değer | Açıklama 
------------ | -------------|----------
operation.silentpn.db.ofuser.modeBase  |1000 | Kullanıcılar bu değer baz alınarak indexlenir ve DB den bu şekilde alınır.
operation.silentpn.db.ofuser.inactivedays | 7 | Uygulamanın son kapanma süresi. Bu süre daha uzun zamandır uygulamasını açmayan kullanıcılar üzerinde yapılır.
xmpp.kafka.broker  |172.18.182.81:9093,172.18.182.81:9094 | kafka Broker Listesi
xmpp.pn.topic  |test-pn-topic| Bildirimler Avro formatında bu topic'e beslenir.
pn.slient.dispacher.cron  |0 0 */7 * * | [cron format񠩮celeyin ](http://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/support/CronSequenceGenerator.html)


**Silent Pn Gönderilecek Kullanıcıları kullanan SQL:**
```
select username, ostype,osversion, pntoken, voip_pntoken, cert_type, appversion from ofuser where username IN (SELECT username FROM OFPRESENCE where TO_DATE ('1970-01-01 00', 'yyyy-mm-dd hh24')+ (OFFLINEDATE +10800000)  / 1000 / 60 / 60 / 24 < SYSDATE -200) and usergroup='USER' and frozen=0
```

Silent PN Hazırlanırken kullanılan Pojo aşağıdaki gibi olacak. **koyu** renkte olan alanlar ön tanımlı diğer alanlar veritabanında gelecek bilgiler ile doldurulur.
* **badge = 1**
* ostype
* osversion
* pntoken
* voippntoken
* **msgtype = "TOV"**
* **notificationtype = "O"**
* **text = "."**
* **sender = "OSS Token Validation"**
* receiver
* **jid = "905320000001"**
* certtype
* appversion
* **service=true**

@ttahkarakaya

## Sürümlerler
### 1.0.0
İlk sürüm temel özellikler yukarıda anlatılmıştır.
