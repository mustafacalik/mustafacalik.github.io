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
.2022
08.03.2022
Gelen Havalemız aşamada testler testiklenmeli ve çalışmalıdır. Bu sayede kodumuz testten geçmeden main branch'a merge edilmemiş olur.

* CI/CD pipelina'larında otomatik olarak testlerin koşulması.

* Production ortamında Periyoduik olarak testlerin koşması ve bunların monitör edilmesi.
