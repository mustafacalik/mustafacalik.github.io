---
layout: post
title: Apigateway
date: 2022-08-02 01:33:20 +0300
description: Apigateway
img: i-rest.jpg # Add image post (optional)
fig-caption: # Add figcaption (optional)
categories: Java
tags: [Apigateway]
---


### Apigateway Nedir ?

Dışarıdan sisteme tek noktadan giriş yeridir. Diğer bir deyişle api yönetim sunucusu olarak da düşünebiliriz. Client'lar için mikroservislere giriş noktasıdır. User'in request'ini aldıktan sonra bunu bir veya daha fazla backend servisine yönlendirip, response'da döneceği datayı hazırlayıp user'a teslim eder. Reverse proxy gibi davranırlar. Design Pattern'lardan Facade pattern gibi de düşünülebilir.


#### Reverse Proxy Nedir ?
Bilgisayar ağında backend sunucuların önünde bulunup, client'lardan gelen istekleri backend sunuculara yönlendiren sunucudur.

![img1](/images/apigateway/reverse-proxy.png)


### Başıca özellikleri:

#### Routing

#### Rate Limiting

#### Load Balancing

#### Service Discovery

#### Monitoring, Logging, Analytics

#### Authentication, Authorization and Fault Tolerance
Tek noktadan giriş yapılmasına imkan verdiği için güvenlik gereksinimlerini daha kolay sağlayabiliriz.

#### Api composition
Fatklı servisleri / mikroservisleri çağırıp bunların response'larını in-memory join yapıp client'a sunabilir.

#### Static Content
Static content sunmak için web server olarak kullanılabilir. Bu sayede static içerikler için backend server'ların üzerine düşen yükü almış olur.

#### Response Caching
Servislerin cachelenmesine imkan verip, backend server yükünü almış olur.

#### Compression
Bandwith ihtiyacının azaltmak için web server ile reverse proxy sunucusu arasında request'lerin sıkıştırılmasını veya çözülmesini sağlayabilir.

#### SSL Termination
Request'i https olarak ele alıp, decrypt ettikten sonra unencrypted request'ler olarak server'lara gönderebilir. Bu sayede birçok web server'a sertifika kurma ihtiyacı ortadan kalkar. SSL/TLS'i tek noktandan yönetmemizi sağlar.

#### Url Rewriting
Url'lerimizi backend'e gelmeden istediğimiz şekilde yeniden belirlememize imkan verir.



#### Kaynaklar

* https://geekflare.com/api-gateway/


* https://www.slideshare.net/AlbertLombarte1/krakend-api-gateway

* https://dzone.com/articles/benefits-reverse-proxy





