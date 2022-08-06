---
layout: post
title: Java'da Thread Dump Nasıl Alınır
date: 2022-08-02 01:33:20 +0300
description: Thread Dump
img: i-rest.jpg # Add image post (optional)
fig-caption: # Add figcaption (optional)
categories: Java
tags: [Thread Dump, Deaclock]
---


Thread'lerin belirli bir zamandaki resmini çekip incelemenizi sağlar. Bu şekilde Thread'ler ile ilgili tüm bilgilere erişebiliriz. Problemleri görmemize, jvm ve uygulama peformansını artırmamıza imkan sağlar. Deadlock oluşumu varsa bunu da görmemizi sağlar. 


#### Deadlock Nedir ?
İki veya daha fazla Thread'in erişmeye çalıştıkları kaynakların birbirleri tarafından bloklanmasından dolayı birbirlerini sonsuza dek beklemeleridir.


#### Deadlock Örneği

Üç adet thread oluşturacağız. Bunlardan bir tanesi deadlock durumuna dahil olmayıp yalnızca ekrana kendi ismini basacaktır. Diğer iki thread ise kullanacakları ortak objeleri lock'layıp deadlock'a sebep olacaklardır.

```java
public class TreadDeadLock {

    public static void main(String[] args) throws InterruptedException {
        String object1 = "Object1";
        String object2 = "Object2";

        Thread thread1 = new Thread(new ExampleThread(object1, object2),"Thread 1");
        Thread thread2 = new Thread(new ExampleThread(object2, object1),"Thread 2");
        Thread thread3 = new Thread(new Runnable() {
            @Override
            public void run() {
                while(true){
                    System.out.println("Thread 3 ...");
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        throw new RuntimeException(e);
                    }
                }
            }
        }, "Thread 3");

        thread1.start();
        thread2.start();
        thread3.start();
    }

}
```

Thread1 object1'i elde ederken locklar ve bir süre bekleyip object2'yi locklamak için bekler. Thread1'in bekleme esnasında Thread2, object2'yi locklar ve object1'i locklamak için bekler. Bekleme zamanlarında diğer thread ihtiyaç duyacağı objeyi zaten locklamış olacağı için iki thread de sonsuza kadar birbirini bekler ve deadlock durumu oluşur.

```java
public class ExampleThread implements Runnable{
    private Object object1;
    private Object object2;

    public ExampleThread(Object o1, Object o2){
        this.object1 =o1;
        this.object2 =o2;
    }
    @Override
    public void run() {
        String name = Thread.currentThread().getName();
        print(name + " START ");
        print(name + " begin to lock --> " + object1);
        synchronized (object1) {
            print(name + " locked --> " + object1);
            sleep();
            print(name + " begin to lock --> " + object2);
            synchronized (object2) {
                print(name + " locked --> " + object2);
            }
            print(name + " lock relased --> " + object2);
        }
        print(name + " lock released --> " + object1);
        print(name + " FINISH ");
    }
    private void sleep() {
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
        }
    }

    private void print(String message){
        System.out.println(message);
    }
}
```

#### Program Çıktısı

Programı çalıştırdığımızda Thread1'in object2'yi, Thread2'nin ise object1'i beklediğini görürüz. Thread3 ise deadlock durumuna girmeksizin bağımsız bir şekilde kendi adını ekrana basar.

![img1](/images/threaddump/code_output.png)

#### Thread Dump nasıl alınır ?


Önce Linux üzerinde ```jps -l``` komutu kullanarak çalışan process'leri görüntüleriz ve uygulamamıza ait process id'yi elde ederiz.

![img1](/images/threaddump/jps.png)


```jcmd``` komutunu kullanarak threaddump'ı threadDump.txt adında bir dosyaya alırız.

```
jcmd 78858 Thread.print > threadDump.txt
```


Oluşan txt dosyasına baktığımızda 3 Thread'den iki tanesinin **BLOCKED** durumunda olduğunu görürüz. 

![img1](/images/threaddump/dump1.png)

Threadlerimizin hangi kod satırında beklediğini bilgisine de aşağıdaki şekilde ulaşabiliriz. Çıktının en altında da 1 adet deadlock bulunduğu bilgisini görüntüleriz.

![img1](/images/threaddump/dump2.png)


Uygulamaya ait kodlara [Github](https://github.com/mustafacalik/blog-apps/tree/main/threaddump)  adresinden ulaşabilirsiniz.
