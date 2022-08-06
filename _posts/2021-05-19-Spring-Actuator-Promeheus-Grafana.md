---
layout: post
title: Spring Boot Uygulama Metriklerinin Prometheus ve Grafana ile Takip Edilmesi
date: 2021-05-19 01:33:20 +0300
description: Spring Boot Uygulama Metriklerinin Prometheus ve Grafana ile Takip Edilmesi # Add post description (optional)
img: i-rest.jpg # Add image post (optional)
fig-caption: # Add figcaption (optional)
categories: Java
tags: [Spring Boot, Spring Actuator, Prometheus, Grafana]
---


Bu yazıda Spring Boot uygulamamızda Spring Actuator ile birtakım metrikleri okuyup, bunları time series bir veritabanı olan Prometheus'a 
aktaracağız. Daha sonra bunları Grafana üzerinden nasıl izleyebileceğimizi göreceğiz.


Öncelikle Spring Actuator'ı projemize nasıl dahil edeceğimizi görelim. Ardından Spring Actuator kullanımı ile ilgili kısa birkaç bilgi verelim. 

Actuator'ı projemize dahil etmek için aşağıdaki dependency'i maven pom.xml'ine ekliyoruz.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Uygulamamızı çalıştırdıktan sonra actuator servis ucuna aşağıdaki şekilde erişebiliriz.

http://localhost:8080/actuator


![img1](/images/b2/img1.png)

Varsayılan olarak actuator altında /health ve /info servis uçları yer almaktadır.
Diğer bilgileri görüntüleyebileceğimiz servisleri de açmak için spring application properties'a aşağıdaki parametreyi ekleriz.


```xml

//Spesifik bilgileri göstermek için
management.endpoints.web.exposure.include=health,info,prometheus,metrics

//Tüm bigileri gçstermek için
management.endpoints.web.exposure.include=*

//loggers bilgilerini göstermemek için
management.endpoints.web.exposure.exclude=loggers
management.endpoint.loggers.enabled=false

```


/metrics ucunu çağırdığımızda karşımıza metrik listesi gelir

http://localhost:8080/actuator/metrics


```json
{
    "names": [
        "http.server.requests",
        "jvm.buffer.count",
        "jvm.buffer.memory.used",
        "jvm.buffer.total.capacity",
        "jvm.classes.loaded",
        "jvm.classes.unloaded",
        "jvm.gc.live.data.size",
        "jvm.gc.max.data.size",
        "jvm.gc.memory.allocated",
        "jvm.gc.memory.promoted",
        "jvm.gc.pause",
        "jvm.memory.committed",
        "jvm.memory.max",
        "jvm.memory.used",
        "jvm.threads.daemon",
        "jvm.threads.live",
        "jvm.threads.peak",
        "jvm.threads.states",
        "logback.events",
        "process.cpu.usage",
        "process.files.max",
        "process.files.open",
        "process.start.time",
        "process.uptime",
        "system.cpu.count",
        "system.cpu.usage",
        "system.load.average.1m",
        "tomcat.sessions.active.current",
        "tomcat.sessions.active.max",
        "tomcat.sessions.alive.max",
        "tomcat.sessions.created",
        "tomcat.sessions.expired",
        "tomcat.sessions.rejected"
    ]
}

```



Burdaki metriklere ilaveten kendimiz de başka metrikler ekleyip, bunların izlenmesini sağlayabiliriz.
Metriklere ait verileri Grafana üzerinden izleyebilmek için öncelikle verileri time series bir veritabanı izleme aracı olan Prometheus'a aktarmamız gerekiyor.


Spring Actuator’ın oluşturduğu metrikleri Prometheus’un okuyabileceği formata çevirmek için micrometer kütüphanesini kullanıyoruz.

```xml
 <dependency>
     <groupId>io.micrometer</groupId>
     <artifactId>micrometer-registry-prometheus</artifactId>
  </dependency>
```


micrometer kütüphanesini de ekleyip, uygulamamızı yeniden başlattığımızda actuator altında /prometheus servis ucunu da görmeye başlarız.

http://localhost:8080/actuator/prometheus


![img3](/images/b2/img3.png)


micrometer kütüphanesi ile kendi metriklerimizi de Counter, Gauge veya Timer tanımlayrak ekleyebiliriz.

Aşağıdaki örnekte saniyede 1 kez çalışacak ve kendi tanımladığımız iki metrik için değerler üretecek bir schedule tanımlıyoruz.


```java
@Component
public class CustomMetricScheduler {

    private final AtomicInteger testGauge;
    private final Counter testCounter;

    public CustomMetricScheduler(MeterRegistry meterRegistry) {
        testGauge = meterRegistry.gauge("custom_gauge", new AtomicInteger(0));
        testCounter = meterRegistry.counter("custom_counter");
    }

    @Scheduled(fixedRateString = "1000", initialDelayString = "0")
    public void schedulingTask() {
        testGauge.set(CustomMetricScheduler.getRandomNumberInRange(0, 100));
        testCounter.increment();
    }

    private static int getRandomNumberInRange(int min, int max) {
        return new Random().nextInt((max - min) + 1) + min;
    }
}

```


/prometheus servis ucunu çağırdığımızda, tanımladığımız yeni metrikleri de burada görmeye başlarız.

http://localhost:8080/actuator/prometheus


![img2](/images/b2/img2.png)


Bundan sonra Spring uygulamamız altında yer alan /prometheus servis ucunu Prometheus'a tanımlayıp, buradaki metrikleri görmesini sağlayabiliriz.


Prometheus uygulamasını docker hub üzerinden indiririz.

![img4](/images/b2/img4.png)


Öncelikle Prometehus için bir konfigürasyon dosyası oluşturmamız gerekiyor. Kendi belirlediğiniz bir dizin altında aşağıdaki gibi konfigürasyon dosyasını oluştururuz.

![img5](/images/b2/img5.png)


![img6](/images/b2/img6.png)


Prometheus uygulamasını konfigürasyon dosyamızı görecek şekilde "docker run" komutu ile çalıştırırız.

![img7](/images/b2/img7.png)


Container'da bulunan uygulamamızın durumunu görmek için de "docker container ls" komutunu kullanırız


![img8](/images/b2/img8.png)


Prometheus uygulamasını docker üzerinden çalıştırdıktan sonra aşağıdaki adresten metrik verilerinin gelmeye başladığını gözlemleyebiliriz.

http://localhost:9090/


![img9](/images/b2/img9.png)


Şimdi sıra Prometheus üzerinde biriken verilerin Grafana ile gözlemlemeye geldi.
Grafana, Prometheus'a kıyasla bize verinin görselleştirilmesi anlamında çok daha fazla seçenek sunar.
Prometheus verinin nasıl saklanacağı ve sorgulanacağı konularında uzmanlaşmışken, Grafana yalnızca verinin gösrselleştirilmesi ile ilgilenir.


Docker üzerinden Grafana'yı da indirip, çalıştırırız.

![img10](/images/b2/img10.png)


Grafana'yı aşağıdaki url'den açarız ve veri kaynağı olarak Prometheus'u seçeriz. Grafana login ekranında "username" ve "password" bilgilerini "admin" olarak gireriz.

Dashboard üzerine eklediğimiz bir panel ile Spring uygulamamız ait metrikleri grafikteki gibi zamana bağlı olarak görüntüleyebiliriz.


![img11](/images/b2/img11.png)

![img12](/images/b2/img12.png)


![img13](/images/b2/img13.png)

![img14](/images/b2/img14.png)


![img15](/images/b2/img15.png)

Uygulamaya ait kodlara [Github](https://github.com/mustafacalik/blog-apps/tree/main/spring-app-monitor-master)  adresinden ulaşabilirsiniz.



Bu yazıda kendi hazırladığımız metrikler dahil birtakım verileri önce Prometheus'a aktarmayı, sonrasında da Grafana üzerinden nasıl izleyebileceğimizi görmüş olduk.
Sonraki yazımda Arduino ile sensörler vasıtasıyla elde ettiğimiz verileri, benzer mantıkla Grafana üzerinden nasıl sunacağımızı göreceğiz.

Örneğin balkonumuzda bulunan bir saksı çiçeği için, ortamın sıcaklığını, nemini, aydınlık seviyesini, havanın kalitesini ve toprağın nemini canlı olarak
Grafana üzerinde grafikler aracılığıyla izleyebiliyor olacağız.
