---
layout: post
title: Scheme İpuçları
---

Bu sene Programlama Dilleri dersinde ödevleri Scheme ile yapıyoruz. Birinci ödevin sınıfın bir kısmı tarafından yapılmadığı gerçeği beni şaşırttı ve üzdü. Bu yazıda size Scheme ile alakalı birkaç ipucu vereceğim. Umarım faydasını görürsünüz.

## 1. REPL Dostunuzdur ##

REPL olan dilleri kullanmak exploratory programming (keşif amaçlı programlama?) yapmak için çok uygun. Dilin kendisini de bu yolla keşfedebilirsiniz.

Yazının ilerleyen kısımlarında başına > sembolü koyduğum kısım REPL'a yazdığım kod parçasını, bir altındaki satır ise çıktısını temsil edecektir.

## 2. Atom, Cons Cell ve Liste Kavramlarını Anlamak Çok Önemli ##

### 2.1 Atomlar ###

Scheme'i oluşturan en temel yapılar atomlardır.  
Sayılar: `45`  
Karakterler: `#\f`  
Stringler: `"hello"`  
Semboller: `'a`  
Boolean: `#t` yada `#f`  
Boş liste: `'()`  

### 2.2 Cons Cell ###

Atomları ve Listeleri cons cell'ler ile birbirine bağlayabiliyoruz.

{% highlight scheme %}
> (cons 1 2)
(1 . 2)
{% endhighlight %}

Bu kodun verdiği çıktıya dotted pair deniyor. Cons ile farklı tipteki verileri de birbirine bağlayabiliyoruz.

{% highlight scheme %}
> (cons "hello" 12)
("hello" . 12)
{% endhighlight %}

### 2.3 Listeler ###

Listeleri linked list olarak düşünebiliriz. Listeleri 2 şekilde oluştururuz (daha fazla yolu var fakat biz genellikle böyle oluşturacağız):  

{% highlight scheme %}
> (list 1 2 3 4)
(1 2 3 4)
> (cons 1 (cons 2 (cons 3 (cons 4 '()))))
(1 2 3 4)
{% endhighlight %}

Hem listeler hemde dotted pairler üzerinde **car** ve **cdr** ismindeki fonksiyonları çağırabiliriz. car listenin ilk elemanını, cdr ise listenin geri kalanını döndürür.

{% highlight scheme %}
> (car '(1 2 3 4))
1
> (cdr '(1 2 3 4))
(2 3 4)
{% endhighlight %}

Fark ettiyseniz car **ilk elemanı**, cdr ise **ilk eleman dışındaki listeyi** döndürdü. Ayrıca ' sembolünü (quote) kullandım. Bu sembol interpreter'a listenin bir fonksiyon olarak algılanmamasını ve çalıştırılmamasını tembihliyor. Yani bu listeyi data olarak kullanmış oluyoruz.

## 3. If ve Cond İfadeleri ##

Bu ifadelerin yapısını bildiğimiz zaman imperative dillerdeki karşılıkları sayesinde çok rahat kavrayabiliyoruz. If special formu(evet special form dedim fonksiyon değil. bunun detayına girmeyeceğim zira anlatması çok uzun sürer.) imperative dillerden bildiğimiz if-else statement'ı. içerisinde elseif bulunmuyor. Cond ise if-elseif-else yapısı gibi. Birçok condition var ve yapacağımız işlemler belirtiliyor. Fakat if ve cond arasında ufak bir fark var. If yalnızca 1 adet fonksiyon alıyor. Cond'a ise istediğimiz kadar fonksiyon yazabiliyoruz. Diyeceksiniz ki bir if bloğunun içinde birden fazla fonksiyon kullanmanın yolu yok mu? Var. Onuda açıklayacağım ilgili kısımda. Gelelim örneklere.

### 3.1 If ###

{% highlight scheme %}
> (if (< a b)
      a
      b))
{% endhighlight %}

a < b ise a değilse b döner. Oraya bir fonksiyon yazıp dönüş değerini de alabilirdik. Şöyle ki:

{% highlight scheme %}
> (if (< a b)
      (run-when-true)
      (run-when-false))
{% endhighlight %}

Bu durumda da run-when-true fonksiyonundan dönen değer if'in doğru olması durumunda elimizde olacaktı. Fakat birden fazla fonksiyonu çalıştırıp. Bir değer elde etmek istiyorsak. Begin yapısını kullanmamız yerinde olacaktır:

{% highlight scheme %}
> (if (< a b)
      (begin
        (do-this)
        (do-that)
        (return-that))
      b)
{% endhighlight %}

Bu durumda a < b olduğunda önce do-this sonra do-that ardından return-that fonksiyonu çalıştırılır. Dönen değer de return-that fonksiyonundan dönen **değer** olacaktır(Değeri vurgulamamın sebebi fonksiyonun **kendisini** de döndürebiliyor olmamız. Kendisini saran parantezleri kaldırdığımızda fonksiyon döndürmüş oluruz.) Sanırım if'i yeterince açıkladım. Cond'a geçelim.

### 3.2 Cond ###

Kodunu yazmaya üşendiğim için hazır bir kod parçası üzerinden açıklayacağım. Bu kod parçasını [Yet Another Scheme Tutorial](http://www.shido.info/lisp/idx_scm_e.html)'in [5. Branching](http://www.shido.info/lisp/scheme5_e.html) kısmından aldım.

{% highlight scheme %}
(define (fee age)
  (cond
   ((or (<= age 3) (>= age 65)) 0)
   ((<= 4 age 6) 0.5)
   ((<= 7 age 12) 1.0)
   ((<= 13 age 15) 1.5)
   ((<= 16 age 18) 1.8)
   (else 2.0)))
{% endhighlight %}

cond'dan sonraki ilk satırda age değerinin 3 ten küçük eşit ve 65 ten büyük eşit olup olmadığına bakıyor. Öyleyse 0 döndürüyor. Sonraki satırda age 4 ile 6 aralığındaysa 0.5 döndürüyor. Diğer ifadelerde buna benziyor zaten. En son satırında ise else var. Hiçbir koşul sağlanamadıysa else'teki ifade dönüyor.

Burada değinmek istediğim bir nokta var.    

{% highlight scheme %}
(<= 4 age 6)
{% endhighlight %}

Şu ifade ne kadar güzel öyle değil mi? Çok sade. C'de bunu şöyle yazmalıydık.

{% highlight c %}
4 <= age && age <= 6
{% endhighlight %}

Neden böyle yapmalıydık? 4 <= age <= 6 şeklinde yazamaz mıydık dediğinizi duyar gibiyim. Eğer merak ediyorsanız neden böyle yapmamanız gerektiğini araştırmanızı istiyorum. (Bunu anlatmak bu yazının konusu olmadığı için es geçtim)

Bu yazıyı burada noktalıyorum. Yazıda bir hata varsa beni uyarabilir yada github'dan gerekli değişikliği yapıp pull request gönderebilirsiniz. Eğer beğenen(olacağını pek sanmasamda :D) ve devamının gelmesini isteyen varsa merak ettiği konuları yorum kısmına yazabilir. Facebook ve mail yoluyla da tavsiyelerinizi ulaştırabilirsiniz.
