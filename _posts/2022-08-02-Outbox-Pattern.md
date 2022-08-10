---
layout: post
title: Outbox Pattern
date: 2022-08-10 09:00:20 +0300
description: Outbox Pattern
img: i-rest.jpg # Add image post (optional)
fig-caption: # Add figcaption (optional)
categories: Java
tags: [Outbox Pattern, Debezium, Dual Write, Change Data Capture, Transaction Log Tailing, Polling Publisher]
---

Outbox pattern'ine örnek olarak temelde aşağıdaki resimdeki örneği verebiliriz. Mikroservisimize gelen istek sonucunda, hem kendi veritabanımızda hem de bu istek için atılan event'i dinleyen ilgili mikroservislerde aksiyon alınmak istendiğinde,  event'i publish edemediğimiz durumlarda kaynaklar arasında veri tutarsızlığı meydana gelecektir.

![img1](/images/outbox/outbox.png)


Dual Write problemi de bu senaryo için örnek olarak verilebilir.


### Dual Write
Bir olay tetiklendiğinde aynı veriyi iki farlı yere yazıyorsak burda Dual Write problemi olabilir. Distributed bir ortamda biz bu işlemi yaptığımızda ek önlemler almazsak bu kaynalar arasında tutarsızlık meydana gelecektir. Örneğin mikroservisimiz tetiklendiğinde verinin hem mikroservisin kendi veritabanına hem de bir mesaj kuyruğuna gönderilmesini Dural Write durumuna bir örnektir.

Event'i gönderememiz veya kuyruğa atılan event'in bir şekilde işlenmemesi tutarsızlığa sebep olacaktır.

### Çözüm


Bu gibi problemlere çözüm olarak Outbox Pattern kullanılabilir. Bu çözümde veriyi iki farklı kaynağa göndermek yerine veriyi tek veritabanında aynı trancation içinde atomic olarak hem ilgili tabloda hem de event broker için gerekli olan bilgileri Outbox tabloda tutarız.

Daha sonra Outbox tablo üzerinden bilgileri alıp event'leri publish ederiz. Başarılı publish işleminden sonra da buradaki kayıtları sileriz. Bu sayede ana tabloda yaptığımız işlemle birlikte bunun diğer kaynakta da yapıldığından / işlendiğinden emin olmuş oluruz.



Outbox pattern'in iki faklı şekilde uygulanabilir. Bunlar ***Transaction Log Tailing*** ve ***Polling Publisher*** yaklaşımlarıdır.

### Transaction Log Tailing

Bu yöntem için Change Data Capture (CDC) pattern'inin bir implementasyonu olan Debezium ve Kafka Connect kullanabiliriz.

Outbox tablomuzda değişilik yapıldığında, bunlar Debezium aracılığı ile transaction loglarına bakılarak başka bir kaynağa aktarılır. Bu kaynak için Kafka Connector'i kullandığımızda Outbox tablomuzda yer alan event bilgileri Kafka topic'lerine publish olur.


### Polling publisher

Bu yöntemde Outbox tablomuzda yer alan eventler periyodik olarak çekilip bağlı bulunan event sourcing kaynağına publish edilir. Bu  yöntemde belirli aralıklarla veritabanına gidip sorgu atmak gerektiği içinve veritanına ekstra yük getirdiği için performans bakımından dezavantaj oluşturabilir.





#### Kaynaklar

* https://fullstackdeveloper.guru/2022/05/19/how-to-implement-transactional-outbox-design-pattern-in-spring-boot-microservices/


* https://medium.com/hepsiburadatech/nedir-bu-transactional-messaging-474a793d7780

* https://dzone.com/articles/implementing-the-outbox-pattern  