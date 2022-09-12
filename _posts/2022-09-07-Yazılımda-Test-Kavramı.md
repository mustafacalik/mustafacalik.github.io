---
layout: post
title: Yazılımda Test Kavramı
date: 2022-08-15 01:33:20 +0300
description: Yazılımda Test Kavramı
img: i-rest.jpg # Add image post (optional)
fig-caption: # Add figcaption (optional)
categories: TEST
tags: [unit test, entergration test]
---


Yazılımda test çok önemli bir yere sahiptir. Uygulamamızda çıkan hataların maliyeti geliştirme aşamasında çok az iken, ilerleyen evrelerde misliyle artabilmektedir. Bunu dikkate alarak yazdığımız kodları geliştirme aşamasından başlayarak test edersek, olası hatalar ile karşılaşma ihtimalimizi en aza indirgemiş olacağız ve bu da yazılım maliyetlerini minimum seviyede tutmamızı sağlayacaktır.


![img1](/images/test/unittest-cost.png)


### Unit Test nedir ?
Yazdığımız kodlarda test edilecek en küçük birimdir. Sınıflarımız için ve içindeki metodlar için unit testler yazarız. Bunlar bağımlılıklarından arındırılarak (mocklayarak) test edilir.


### Entegrasyon Testi nedir ?
Unit test yazarken en küçük ve tek parçayı test ederken, entegrasyon testinde unit testte test ettiğimiz parçaların birarada çalışırken test edimesini sağlarız. Mocklama ihtiyacı bulunmaz bunun yerine bağımlılıkları gerçek halleriyle test ederiz. 3 tip entegrasyon testi yaklaşımı vardır.

* Big Bang Approach
* Top Down Approach
* Bottom Up Approach


### Fonksiyonel Test nedir ?
Uygulamamızı bir bütün olarak ele alırız ve tüm modülleriyle beraber gerçek zamanlı test ederiz. Uygulamamızın iş gereksinimlerini, ihtiyaçlarını, sorumluluklarını ve fonksiyonlarını önceden yazılmış olan test case'lerine göre test etmiş oluruz. Projemizdeki tüm  kullanıcıların beklediği şekilde çalıştığını test eder.

### Regresyon Testi nedir ?
Uygulamamızda bir yazılım değişikliği yaptğımızda, bu değişikliğin daha önceden yapılmış ve çalışan olan bir akışı bozup / bozmadığını anlamak için regresyon testleri koşulur. Diğer bir ifadeyle uygulamanın değişmeyen kısımlarının yeniden test edilmesi anlamına gelir.


### Test Driven Development
Kısaca kodu yazmadan testini yazmaktır.


### Unit Test ve Entegrasyon Testi karşılaştırması

* Unit test'lerin koşulması Entegrasyon testine göre daha hızlıdır.
* Entegrasyon testleri güvenirlik bakımından daha doğru sonuçlar verir.
* Unit test yazılımcı perspektifinden bakar, entegrasyon testi ise kullanıcı perspektifinden bakılarak yazılır.
* Unit test white box testing olarak geçerken, entegrasyon testi black box testing olarak tanımlanır.

### Best Practice'ler nelerdir ?

* Kodumuz Main branch'a merge olmadan push yaptığımız aşamada testler testiklenmeli ve çalışmalıdır. Bu sayede kodumuz testten geçmeden main branch'a merge edilmemiş olur.
* CI/CD pipelina'larında otomatik olarak testlerin koşulması.
* Production ortamında Periyoduik olarak testlerin koşması ve bunların monitör edilmesi.



### Unit Test Yazarken Nelere Dikkat Emeliyiz ?


### Varolan kodlarımız ile aynı paket yapısında test classlarımızı oluşturmalıyız.

--> resim

#### İsimlendirmeye dikkat etmeliyiz

Unit testlerimiz için testin amacını açık bir şekilde yansıtan, okunabilir isimler tercih etmeliyiz. Testlerimizin okuyan diğer kişiler tarafından da kolay bir şekilde ne yapıldığı anlaşılıyor olmalı. Anlaşılabilir unit test isimleri kodun daha sonra geliştirilmesi esnasında da bizim ve diğer kişiler için kolaylık sağlayacaktır.

Bazı İsimlendirme Örnekleri:

* MethodAdı_TestEdilenDurum_BeklenenDavranış
  
  isOddNumber_NumberIsOdd_True

* MethodAdı_BeklenenDavranış_TestEdilenDurum
  
  isOddNumber_True_NumberIsOdd

* test[TestEdilenDurum + BeklenenDavranış]
  
  testTrueIfNumberIsOdd
  
* TestEdilenDurum + BeklenenDavranış
  
  trueIfNumberIsOdd
  
* Should_BeklenenDavranış_When_TestEdilenDurum
  
  Should_True_When_NumberIsOdd
  
* When_TestEdilenDurum_Expect_BeklenenDavranış
  
  When_NumberIdOdd_Expect_True
  
* Given_ÖnKoşul_When_TestEdilenDurum_Then_BeklenenDavranış
  
  Given_UserAuthenticated_When_NumberIsOdd_Then_True




#### Kod yapısına dikkat etmeliyiz

Unit testlerimizde best practice olarak test kod blocklarımızı Given/When/then yaklaşımı ile yazarsak bu sayede daha okunabilir unit testlerimiz olur.

Given -> Input olarak kullanacapımız data veya oluşturacağımız mock'ları buraya yazarız.

When -> Test etmek istediğimiz metodu çağırdığımız yerdir.

Then -> Assertion'ları yaptığımız yerdir. Beklenen sonucun gelip gelmediğini veya davranış olarak istediğimi gibi çalışığ çalışmadığını kontrol ettiğimiz yerdir.


---> resim

#### Testleri development esnasında yazmalıyız. Ne kadar erken yazarsak kodumuz o kadar clean olur ve bugları önceden yakalamış oluruz. 


### Unit teslerimiz CI/CD pipelarına bağlı ve otomatik olarak koşulabilir olmalı. 

#### Testlerimiz deterministik olmalı yani test kodumuz değişmedikçe hep aynı tutarlı davranışı göstermeli. 

#### Her bir unit testin yalnız bir durumu test etmesi önerilir. Multiple assertlerden kaçınmalı. Mümkünse bir assertion olmalı, farklı parametreler için parametrik testler kullanılabilir. Bu sayede testimiz fail olduğunda hatanın kaynağı ile ilgili daha açık bir çıkarım elde etmiş oluruz.

#### Unit testlerimizde conditional logic'ler (if, while, switch, for) kullanmamalıyız. Testlerimiz içinde çok fazla logic bulunmamamlı mümkün mertebe kısa ve açık olmalı.

#### Testlerimiz birbirine bağımlı olmamalı. Eş zamanlı olarak birbirinden bağımsız çalıştırılabilmeliler.

#### Birçok kez kullanılan değerleri bir değişkene atamak okunabilirliği düşürmektedir. Okuyan kişi değişkenin nerelerde geçtiğini anlamak durumunda kalabiliyor. Bunun yerine değerleri direkt olarak yazmalıyız. 

#### Yazdığımız unit testlerde maxiumum test coverage'i amaçlamalıyız.

#### Testlerimizde birçok yerde kullandığımız objelerimiz var ise veya aynı şeyleri birçok kez mock^luyorsak, helper fonksyionlar kullanabiliriz. Bu sayede okunabilirliği artırıp, test kodumuzun sürdürülebilirliğini artırmış oluruz.

#### assertionlarda actual ve expexcted takısı ile değişkenlerimizi tanımlarsak okunabilirliği artırmış oluruz ve değişkenlerin amacını da açık olarak göstermiş oluruz. 

#### Test sınıflarımızda kompleks inheritance hiyerarşileri kullanmamalıyız. Bu kullanım çok esnek olmayacaktır ve okuyucunun farklı classlara gözatmasına sebebiyet verecektir. Bunun yerine ortak kullanılacak tanımları @BeforeAll ile tanımlayabiliriz. 


#### Okunabilir test name'ler kullanabiliriz. @DisplayName anotasyonu junit 5 ile kullanılabilir.


#### Testimiz magic number ve magic stringlerden arındırılmış olmalı.