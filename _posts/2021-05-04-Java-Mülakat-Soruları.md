---
layout: post
title: Java Mülakat Soruları
date: 2021-05-05 01:33:20 +0300
description: You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. # Add post description (optional)
img: i-rest.jpg # Add image post (optional)
fig-caption: # Add figcaption (optional)
categories: Java
tags: [Mülakat]
---
Mülakatlarda karşılaşılan java sorularını ve cevaplarını bu yazıda bulabilirsiniz. Yeni sorular ve cevaplar ile güncelliğini koruyan bir paylaşım olmaya devam edecektir.


### 1.  JVM, JDK, JRE nedir ?

* JVM: Java sanal makinesidir. Compile olan java sınıflarımız (.class),  JVM aracılığı ile makina koduna dönüştürülür ve bu sayede platform bağımsız olarak farklı işletim sistemlerinde sorunsuz çalışabilir. Java'nın "Write Once, Run Anywhere" özelliği de buradan gelir.

* JDK: Java geliştirme kitidir. Java ile geliştirme yapmak için bütün araçları içeren bir pakettir.
* JRE: Java uygulamasını çalıştırmak için gerekli olan Java kütüphanelerini ve JVM içerir

### 2.  Java'nın platform bağımsız çalışmasını sağlayan nedir ?
bytecode'dur. Compiler java programını class'a yani bytecode'a çevirir. bytecode platform bağımsız her bilgisayarda çalıştırılabilir.
### 3.  Classloader nedir ?
Class dosyalarını yüklemeye yarayan JVM'in alt sistemidir. Java programını çalıştırdığımızda, program öncelikle classloader ile yüklenir.
Üç çeşit classloader vardır.
* Bootstrap Classloader
* Extension Classloader
* System/Application Classloader

### 4.  Java erişim berlirleyicileri nelerdir ?
* Public:  Her yerden erişilebilir.
* Protected:  Sınıf, paket ve altsınıflar tarafından erişilebilir.
* Default:  Sınıf ve paket içerisinden erişilebilir.
* Private:  Sadece sınıf içerisinden erişilebilir.

### 5.  Static metod ve değişkenlerin kullanım amacı nedir ?
Static; class'a ittir, objeye değil. Static tanımlanan metod ve değişkenler class'ın tüm objeleri için ortak olduğu için hepsi tarafından kullanılabilir.

### 6.  Aşağıdaki programın output'u nedir ?

```java
class Test {
    public static void main(String[] args) {
        System.out.println(1 + 2 + "Test");
        System.out.println("Test" + 1 + 2);
        System.out.println(1 * 2 + "Test");
        System.out.println("Test" + 1 * 2);
    }
}
```
* 3Test
* Test12
* 2Test
* Test2


### 7.  ”==” ve “equals” arasındaki fark nedir ?
“==” referans karşılaştırmasıdır. Her iki nesnenin Memory’de aynı konumu işaret edip etmediğine bakar.
“equals” nesnelerin değerlerini karşılaştırır. Her iki nesne için de hashCode() methodundan dönen değer aynı ise kesinlikle equals true olur.
{% highlight java %}
class Test {
    public static void main(String[] args) {
        String a = new String("String1");
        String b = new String("String1");

        System.out.println(a == b);         //false
        System.out.println(a.equals(b));    //true

        System.out.println(a.hashCode());   //-217105888
        System.out.println(b.hashCode());   //-217105888
    }

}
{% endhighlight %}
