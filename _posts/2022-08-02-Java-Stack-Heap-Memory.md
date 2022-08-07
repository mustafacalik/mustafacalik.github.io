---
layout: post
title: Java'da Stack ve Heap Memory
date: 2022-08-07 01:33:20 +0300
description: Java'da Stack ve Heap Memory
img: i-rest.jpg # Add image post (optional)
fig-caption: # Add figcaption (optional)
categories: Java
tags: [Stack Memoru, Heap Memory]
---


Java'da iki memory kavramı vardır. Bunlar Stack Memory ve Heap Memory'dir.


![img1](/images/stack-heap/heap-stack.jpg)


### Stack Memory

Java'da bir method çağırıldığında stack memory'de yeni bir blok oluşturulur. Bu block içinde local primitive değişkenler ve obje referansları yer alır. Method sonlandığında bu block boşa çıkmış olur ve sonraki çağrılacak method için ulaşılabilir duruma gelir.
Java'da uygulama için ayrılan Stack memory alanı, Heap memory'e kıyasla çok düşüktür.

#### Özellikleri

* Thread'ler için RAM'de runtime da oluşturulan fiziksel bir alandır.
* Primitive değişkenleri, Obje referanslarını tutar.
* Kullanılan memory fonksiyonun sonlanmasıyla boşa çıkar.
* Last-In-First-Out (LIFO) sırasıyla kullanılır.
* Memory'de yer kalmadığında java.lang.StackOverFlowError hatası fırlatılır.
* Heap Memory'e kısyasla çok daha hızlıdır.
* Threadsafe'dir. Her thread yalnız kendi Stack'ine erişebilir.


#### Heap Memory
Runtime'da dinamik olarak Java objeleri ve JRE classları için memory'de tutulan alandır.

#### Özellikleri

* Objeler burada yer alırken, referansları ise Stack memory'de bulunur.
* Memory'de yer kalmadığında java.lang.OutOfMemoryError hatası fırlatılır.
* Stack Memory'e kısyasla çok daha yavaştır.
* Stack Memory gibi kullanılmayan, referansları olmayan objeler otomatik olarak serbest bırakılmaz. Garabage Collector, memory'i daha verimli kullanmak için kullanılmayan objeleri temizler.
* Threadsafe değildir. Objeler global olarak her thread için erişilebilir durumdadır. 
