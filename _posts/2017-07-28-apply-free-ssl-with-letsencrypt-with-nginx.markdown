---
layout: post
title:  "Ubuntu-Nginx 서버에서 Let'sEncrypt를 이용한 무료 SSL 적용방법"
slug: "letsencrypt-with-nginx-on-ubuntu"
date:   2017-07-28 15:44:00 +0900
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

<div class="fb-like" data-href="https://fureweb-com.github.io/blog/2017/07/28/letsencrypt-with-nginx-on-ubuntu.html" data-layout="button_count" data-action="like" data-size="small" data-show-faces="true" data-share="true"></div>
<br>

## 환경  

본 포스트는 다음과 같은 환경에서의 작동을 보장합니다.  
- 서버 운영체제 : Ubuntu 16.04 LTS  
- 웹 서버 : Nginx 1.10  
- 이미 보유한 도메인이 있으며, 해당 도메인에 대한 http 요청 처리를 할 수 있는 Nginx 설정이 이미 되어있음

또한, 이미 Ubuntu 16.04 LTS 버전에 알맞은 Nginx 1.10 이상의 웹 서버를 설치했음을 가정합니다만, 
위 조건보다 버전이 높은 경우는 문제가 없을 것으로 예상됩니다.  

만약 현재 보유한 도메인이 없는 경우 아래 내용을 실습할 수 없으며, 
도메인을 보유하고 있지만 Nginx와의 설정을 어떻게 해야하는지 모르는 경우 아래의 실습을 진행할 수 없습니다.  

향후 도메인을 보유하고 있지만, Nginx와의 설정은 어떻게 해야하는지에 대한 부분은 별도로 포스트한 뒤, 
본 게시글에 링크로 연결될 수 있도록 하겠습니다.


## 전체 과정

위와 같은 환경에서 특정 도메인에 SSL을 적용하기 위한 과정은 다음과 같습니다.

- certbot 설치
- SSL 인증서 발급
- Nginx 설정
- 자동 갱신 설정

위 과정별로 무엇을 어떻게 해야하는지 하나 하나 알아보도록 하겠습니다.

## 세부 과정별 설명

# 1. certbot 설치  

운영체제에 certbot을 설치합니다. apt-get을 통해 설치할 수 있도록 certbot의 저장소를 추가합니다.

{% highlight bash %}
$ sudo add-apt-repository ppa:certbot/certbot
{% endhighlight %}  

해당 저장소에 담긴 패키지 정보를 확인할 수 있도록 다음과 같이 업데이트를 수행합니다.

{% highlight bash %}
$ sudo apt-get update
{% endhighlight %}

`apt-get`을 이용하여 Certbot을 설치합니다.
{% highlight bash %}
$ sudo apt-get install python-certbot-nginx
{% endhighlight %}


# 2. SSL 인증서 발급

설치한 Certbot을 이용해서 도메인에 대한 인증서를 발급을 받겠습니다. 아래 예제로 사용된 도메인을 자신이 보유한 도메인으로 변경해주세요.
{% highlight bash %}
# fureweb.com에 대한 SSL 인증서만 발급합니다.
# 2017/7 기준, 아직 와일드카드를 지원하지 않습니다.
$ sudo certbot certonly --nginx -d fureweb.com
{% endhighlight %}

-d 옵션을 계속 나열하여 복수개의 도메인에 대한 SSL 인증서를 한 번에 발급받을 수도 있습니다.
{% highlight bash %}
# 동시에 여러개의 SSL 인증서만 발급합니다.
$ sudo certbot certonly --nginx -d fureweb.com -d sub1.fureweb.com -d sub2.fureweb.com
{% endhighlight %}

발급받은 SSL 인증서는 다음의 경로에 저장됩니다.
{% highlight bash %}
$ cd /etc/letsencrypt/live/fureweb.com
{% endhighlight %}

해당 도메인에 대해 총 5개의 파일이 생성되어있음을 확인할 수 있습니다.  
`cert.pem`, `chain.pem`, `fullchain.pem`, `privkey.pem`, `README`

# 3. Nginx 설정

Certbot을 통해 설치된 파일 중 다음의 두 개의 파일을 Nginx 설정에서 사용함을 명시해줍니다.  
`fullchain.pem`, `privkey.pem`

해당 도메인과 연결된 server 블록을 다음과 같은 형태로 작성합니다.
{% highlight bash %}
server {
    # fureweb.com으로 들어오는 http 요청에 대해 https://fureweb.com으로 리다이렉트 처리합니다. 
    listen 80;
    server_name fureweb.com;
    return 301 https://fureweb.com;
}

server {
    listen 443;
    server_name fureweb.com;

    ssl on;
    ssl_certificate /etc/letsencrypt/live/fureweb.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/fureweb.com/privkey.pem;

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers HIGH:!aNULL:!MD5;
    # 그 외 현재 도메인과 연결된 document_root 등에 대한 설정 기술
}
{% endhighlight %}

설정파일을 위와 같이 수정한 뒤, nginx의 설정에 오류가 없는지 확인해본 뒤 재시작 없이 설정을 적용시킵니다.
{% highlight bash %}
# 설정 파일에 오류가 없는지 확인합니다.
$ sudo nginx -t
# 오류가 없음을 확인한 뒤, 프로세스의 재시작 없이 최신 설정을 적용합니다.
$ sudo systemctl reload nginx
{% endhighlight bash %}

잘 적용이 되었는지, 웹브라우저를 통해 http://fureweb.com 으로 접속 해 봅니다.


# 4. 자동 갱신 설정

위의 모든 과정이 정상적으로 진행된 경우, 갱신에 대해 자동으로 반복하도록 crontab에 해당 스크립트를 등록할 수 있습니다.

{% highlight bash %}
# 기본 설정된 에디터로 crontab을 수정합니다.
$ sudo crontab -e
# crontab에 다음의 한 줄을 추가한 뒤 저장&종료합니다.
# 매일 새벽 2시에 아래의 갱신 작업을 수행하도록 합니다. (원하는 스케쥴로 변경하셔도 됩니다.)
0 2 * * * /usr/bin/certbot renew --quite
{% endhighlight bash %}


## 마치며

예전에 SSL 관련 게시글을 작성했을 때에는 Certbot이라는 것이 없어서 매우 복잡한 과정을 통해 
설정을 마칠 수 있던 것으로 기억합니다.  

이제 Certbot을 통해 편하게, 내 도메인에 SSL을 적용해 보세요!
  
  
<div class="fb-comments" data-href="https://fureweb-com.github.io{{page.url}}" data-width="100%" data-numposts="10"></div>

<div id="fb-root"></div>
<script>(function(d, s, id) {
  var js, fjs = d.getElementsByTagName(s)[0];
  if (d.getElementById(id)) return;
  js = d.createElement(s); js.id = id;
  js.src = "//connect.facebook.net/ko_KR/sdk.js#xfbml=1&version=v2.10&appId=403216550080274";
  fjs.parentNode.insertBefore(js, fjs);
}(document, 'script', 'facebook-jssdk'));</script>