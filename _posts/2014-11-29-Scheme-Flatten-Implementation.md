---
layout: post
title: Scheme - Flatten Implementation
---

## Flatten Nedir? ##

Öncelikle flatten tam olarak ne işe yarar kısa bir hatırlatmış olayım. Elimizde  
((1) 2 ((3 4) 5) ((())) (((6))) 7 8 ())  
gibi nested bir list olsun. Flatten bu listeyi alıp  
(1 2 3 4 5 6 7 8)  
şeklinde bütün value'ları aynı derinliğe indiren bir fonksiyon.

## Bildiklerimiz ##

Şimdi elimizde neler var bir bakalım.

1. Flatten'ın parametre olarak liste aldığını biliyoruz. Muhtemelen nested bir liste.
2. null? fonksiyonunun sadece boş liste aldığında #t döndürdüğünü biliyoruz.
3. pair? fonksiyonunun verilen değişkenin dotted pair(car ve cdr kısımları olan yapılar) olup olmadığını kontrol ettiğini biliyoruz. Bu bilgi neden önemli? Çünkü listeler de bir dotted pairdir aslında. Şöyle ki:
{% highlight scheme %}
(equal? '(1 2 3) 
        '(1 . (2 . (3 . ()))))
;; #t sonucunu alırız {% endhighlight %}
Dolayısıyla bir değişkenin **'(1 2 3)** gibi bir liste mi yoksa **2** gibi bir değer mi olduğunu **not** ve **pair?** fonksiyonları yardımı ile öğrenebiliyoruz.  
4. append fonksiyonunun 2 **listeyi** birbirine ekleyip döndürdüğünü biliyoruz.
    - Ayrıca herhangi bir liste ile boş listeyi append edersek elimizde ilk listenin aynısı olur. Dolayısıyla append fonksiyonuna parametre olarak boş liste vermekten korkmuyoruz.

## Algoritma ##

Bu bilgiler ışığında ortaya bir algoritma koymamız lazım. Flatten algoritmamızı şu şekilde tasarlıyoruz:

1. Fonksiyon parametresi boş liste ise boş liste döndür.
2. Fonksiyon parametresi bir değere karşılık geliyorsa (yani liste değilse) kendisini bir listeye sarmalayıp döndür.
3. Fonksiyon parametresi boş liste veya bir değer değilse, içinde eleman olan bir listedir. Bu listenin:
    1. İlk elemanını(car) flatten algoritmasından geçir.
    2. Geri kalan elemanlarını(cdr) flatten algoritmasından geçir.
    3. (1) ve (2) den dönen değerleri append edip döndür.

## Implementasyon ##

Bunu Scheme dilinde implement ettiğimizde rosetta code'daki implementasyona[^1] yakın bir şey elde etmemiz gerekir. O da şu şekilde:

{% highlight scheme %}
(define (flatten x)
    (cond ((null? x) '())
          ((not (pair? x)) (list x))
          (else (append (flatten (car x))
                        (flatten (cdr x)))))) {% endhighlight %}

## Sonuç ##

Önemli olan recursive fonksiyonumuzdaki base caseleri net bir şekilde ifade edebilmek ve üstünden geçilmemiş bir ihtimal bırakmamak. Bunu sağladığımızda recursive call'un düzgün çalışacağından emin oluyoruz.

Umarım bu yazı faydalı olmuştur. Her türlü geri bildirim için bana ulaşın.

[^1] Referans aldığım implementasyon [rosetta code, flatten implementasyonu](http://rosettacode.org/wiki/Flatten_a_list#Scheme)
