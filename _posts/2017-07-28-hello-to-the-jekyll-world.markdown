---
layout: post
title:  "jekyll을 사용한 첫 게시글"
date:   2017-07-28 12:12:02 +0900
categories: blog
---
<!-- Global site tag (gtag.js) - Google Analytics -->
<script async src="https://www.googletagmanager.com/gtag/js?id=UA-121955159-1"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());

  gtag('config', 'UA-121955159-1');
</script>
<script async src="//pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
<!-- fureweb-github -->
<ins class="adsbygoogle"
     style="display:block"
     data-ad-client="ca-pub-6234418861743010"
     data-ad-slot="8427857156"
     data-ad-format="auto"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

마크다운을 이용하여 `예쁜` 블로그 포스팅을 할 수 있습니다.

jekyll을 이용하면 다음과 같이 코드 스니펫도 예쁘게 작성할 수 있습니다.

아래는 javascript
{% highlight javascript %}
// 주석이 제대로 출력이 됩니까?
function printHellowWorld() {
    console.log('hello world!')
}
{% endhighlight %}

아래는 bash
{% highlight bash %}
ssh -p port root@test.com
{% endhighlight %}

아래는 php
{% highlight php %}
<?php phpinfo(); ?>
{% endhighlight %}

아래는 html
{% highlight html %}
<html>
  <head></head>
  <body>
    <h1>Hello World!</h1>
  </body>
</html>
{% endhighlight %}

아래 링크는 다음과 같은 문법을 통해 작성할 수 있습니다.
{% highlight jekyll %}
기존 블로그 링크는 [퓨어웹 블로그][fureweb] 링크를 통해 접근할 수 있습니다.

[fureweb]: http://fureweb.com
{% endhighlight %}


기존 블로그 링크는 [퓨어웹 블로그][fureweb] 링크를 통해 접근할 수 있습니다.

[fureweb]: http://fureweb.com


## jekyll 짱좋네요!