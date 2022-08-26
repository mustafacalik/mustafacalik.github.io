---
layout: post
title: RabbitMQ ve Kafka İncelemesi
date: 2022-08-15 01:33:20 +0300
description: RabbitMQ-ve-Kafka-İncelemesi
img: i-rest.jpg # Add image post (optional)
fig-caption: # Add figcaption (optional)
categories: Message Broker
tags: [rabbitmq, kafka, broker]
---


Her ikisi de bir mesage broker'dir. Message Broker, mesaj kuyruk sistemlerine verilen genel isimdir. Apache Kafka, ActiveMQ, Azure Service Bus gibi çok yaygın olarak kullanılan mesaj kuyruk sistemleri vardır.

## Neden kullanırız ?

Bazı işlerin anlık olarak değil de asenkron olarak yapılabilmesi için mesaj sistemlerini kullanırız. Yoğun istek alan uygulamalarda anlık cevap veremiyorsak veya uzun sürecek maliyetli işleri kullanıcıyı bekletmeden arka planda yapmak için bu yapıları kullanırız. Örnek olarak çok sayıda kullanıcımıza mail göndermek, rapor oluşturmak, pdf oluşturmak. Bunları yanında mikroservis dünyasında servislerin kendi aralarında haberleşmesi için de mesaj kuyruk sistemlerini kullanırız.


## Rabbit MQ

#### Özellikleri 
* Erlang programlama dili ile geliştirilmiştir.
* Smart-broker / dumb consumer modelini kullanır. Mesajın consumer'a iletildiğinden emin olduktan sonra kuyrukta mesajı siler. Mesajı ilettikten sonra broker'a ulaşan bir acknowlerdge sayesinde bunu sağlar.
* Senkron ve Asenkron çalışabilir.
* FIFO (First In First Out) ile çalışır.


### Bileşenleri

* **Publisher**: Kuyruğa mesaj gönderen kısımdır.

* **Consumer**: Kuyruktaki mesajı tüketen kısımdır.

* **Routing Key**: Mesajı kuyruğa yönlendireceğimiz key bilgisidir.

* **Exchange**: Mesajın ilk geldiği yerdir.

* **Excahnge Type**: Mesajın içindeki routing key ile hangi kuyruğa nasıl yönlendirileceğine karar veren kısımdır.



### Exchange Type


#### Direct Exchange
Defaul olarak kullanılan exchange türüdür.Routing key'de yazan kuyruğa direkt olarak ulaşmasını saağlar.

#### Fanaout Excahnge
Routing key'e bakılmaksızın gelen mesajı tüm kuyruklara gönderir.

#### Topic Exchange
Routing Key'e göre filtreleyip ilgili kuyruklara gönderir.

#### Header Exchange
Topic Exchange'e benzer şekilde çalışır. Routing Key yerine header'lar içerir ve bunlar ile eşleşen kuyruklara gönderim yapar.


![img1](/images/message-broker/rabbit-exchange.png)

### Dezavantajları

* Yüksek hacimli mesajlaşma için uygun değildir.
* Consumer'ların online olduğunu varsayar ve acknowledge gelmediği sürece mesajın durumunu beklemede olarak işaretler.


### Notlar

Queue'ları varsayılan olarak **Durable** özelliği false'dur. bu yüzden in memory olarka tutulurlar. true yaptığımızda persistant olur ve broker restart olduğunda kuyruktaki mesajlar kaybolmamış olur.




## Kafka

Kafka bir distributed event streaming platform'dur. Yüksek girişli veri akışları ve yeniden oynatma için optimize edilmiş bir mesaj veri yoludur.

#### Özellikleri:
* Dump-broker / smart consumer modelini kullanır. Yani broken mesajın iletilip iletilmediği ile ilgilenmez ve queue içinde cursor'ın nerede kaldığını bilmez. Consumer bunu takip etmek zorundadır.
* Büyük ölçekli mesajlaşma ihtiyacı olan uygulamalarda ve streaming uygulamalarında kullanılır. Kuyrukta mesajlar persistent olarak saklanır.
* Saniyede milyonlarca mesajı karşılayabilir.


### Bileşenleri


#### Topic
Birbiriyle ilişkili nesnelerin tutulacağı alana verilen isimdir. Veritabanındaki tablolara benzetebiliriz.

#### Partition
Topic içindeki kuyruklar olarak düşünebiliriz. Aynı partitionda verilerin sıralı olarak yazılacağı garantidir. Birçok partititon'ın olması okuma ve yazma işini paralel olarak consumer'lara yaptırabilmeyi sağlar. Hangi mesajın hangi partition'a gideceğine varsayılan olarak rastgele karar verilir; fakat istenirse bir kural da tanımlanabilir.

#### Broker
Topic'lerin çalıştığı sunuculara broker denilir. Genellikle kafka cluster'ları en az 3 broker'dan oluşur. Partition'ların hepsi aynı broker içinde yer almaz. Verinin dağıtık olarak işlenebilmesi için bu şekilde bir yöntem izlenir.


![img1](/images/message-broker/kafka-broker.png)


#### Consumer
Consumer'lar topic'e yazılan veriyi partition'lardan okur. Consumer'lar consumer-group içerisinde yer alır. Bir consumer olsa dahi bunu bir consumer-group içinde düşünebiliriz. Kafka, topic içinde partition'lara gelen mesajları consumer group içinde paylaştırır. 
(round-robin kuralı)



![img1](/images/message-broker/kafka-consumer-group.png)



#### Zookeeper
Broker'ları yönetir. Hangi partition2ların lider olacağına karar verir.


#### Ne zaman kullanmalı ?
* Cursor'ın kuyrukta nerede kaldığını consumer'in bilmesini ve yönetmesini istiyorsak.
* Kuyruktaki bir mesajı tekrar consume edebileceğimiz bir senaryomuz bulunuyorsa. (Varsayılan olarak 1 hafta boyunca mesajlar partitionlar'da saklı kalır)
* Saniyede milyonlarca mesajı karşılayabildiği için büyük çaplı veri akışlarında, streaming yapan uygulamalarda kullanılabilir.


#### Rabbit MQ vs Kafka Farkları
* Rabbit MQ smart broker / dumb consumer iken Kafka dumb broker / smart consumer modelini kullanır.
* Rabbit MQ tararında kuyrukta mesajlar consumer tarafından tüketildekten ve acknowledge ile bildirildiktn sonra kuyruktan silinir. Kafka tarafında ise consumer'in bunu başarılı ve başarısız olarak tüketip tüketmediğine bakılmaksızın belirli bir zamana kadar (queue - retention time) bu mesajlar partition'larda tutulur.
* Rabbit MQ tam olarak kuyruk yapısı gibi çalışır iken, Kafka logları yazan bir araç gibi çalışır. Bu yüzden Rabbir MQ'ya kıyasla çok daha performanslıdır, saniyede milyonlarca mesajı karşılayabilir ve streaming için daha uygundur.
* Rabbit MQ push modelini kullanır yani mesajlar produce edildikten sonra hemen subscribe olmuş consumer tarafına pushlanır. Kafka tarafında ise pull model kullanılır. Consumer istediği offset'teki (cursor'ın kaldığı yer) mesaj için talepte bulunur.




## Spring ile Kafka Örneği