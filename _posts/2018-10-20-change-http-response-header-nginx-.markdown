---
layout: post
title:  "Nginx의 응답헤더 중 Server 속성 변경하기"
slug: "changing-server-properties-in-nginx's-response-header"
date:   2018-10-20 12:53:00 +0900
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

<div class="fb-like" data-href="https://fureweb-com.github.io{{page.url}}" data-layout="button_count" data-action="like" data-size="small" data-show-faces="true" data-share="true"></div>
<br>

![응답헤더의 Server 속성 변경](/assets/img/posts/20181020-nginx-response-header.png){:style="width:100%"}


### 개요
Apache, Nginx 등의 여러가지 http 요청을 처리해주는 웹 서버는 기본 설정에 의해 응답시 헤더에 Server라는 속성으로 현재 어떤 서버가 응답하고 있는지 친절하게 알려줍니다. 하지만 이 응답은 보안에 문제가 있을 수 있습니다. 서버가 어떤 종류인지, X-Powered-By 등 WAS는 무엇인지 버전은 무엇인지를 친절하게 알려주는 경우가 있는데, 이는 취약점이 발견된 프레임워크를 이용했을 때 해커들의 먹잇감이 되기 좋을 수 있어 주의해야 할 필요가 있습니다.

그래서 이러한 최소한의 웹 서버 정보를 해커들에게 알려주지 않기 위한 Nginx 설정을 어떻게 해야하는지, 우분투 서버 기준으로 설정 방법을 확인 해 보겠습니다.

~~사실 알려진다한들 어떻게 하긴 어렵겠지만요 -_-;~~

### nginx-extras 패키지 설치

nginx-extras 패키지를 통해 `more_set_headers` 디렉티브를 설정에 사용할 수 있게 됩니다. 우선 쉘에서 아래 명령어를 통해 설치를 진행합니다.

```bash
sudo apt-get install nginx-extras
```

### nginx 기본 설정파일 변경

/etc/nginx/nginx.conf 파일의 http 블럭 내에 아래의 문장을 삽입합니다.

```bash
http {
  ...
  more_set_headers: 'Server: my-webserver-name';
  ...
}
```

### nginx 설정파일 문법 확인

nginx 재시작 전 다음의 명령을 통해 설정 파일의 문법에 문제가 없는지 확인합니다.
```bash
sudo nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

### nginx 재시작

아래의 명령을 통해 nginx를 재시작합니다.

```bash
sudo service nginx restart
```

### http 응답 확인

아래 명령을 통해 nginx의 http 응답 헤더를 확인합니다.

```bash
curl -I localhost
```

아래와 같은 응답을 확인할 수 있습니다.

```
HTTP/1.1 200 OK
Date: Fri, 19 Oct 2018 10:27:20 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 24610
Connection: keep-alive
ETag: "6022-O5kJCg6irxNIB8CARmIFCRWlcRE"
Vary: Accept-Encoding
Server: my-webserver-name
```

이상 Nginx에서 http 응답 시 헤더에 기본적으로 작성되는 Server 속성을 변경하는 방법을 간단히 확인 해 보았습니다.

<div class="fb-comments" data-href="https://fureweb-com.github.io{{page.url}}" data-width="100%" data-numposts="10"></div>

<div id="fb-root"></div>
<script>(function(d, s, id) {
  var js, fjs = d.getElementsByTagName(s)[0];
  if (d.getElementById(id)) return;
  js = d.createElement(s); js.id = id;
  js.src = "//connect.facebook.net/ko_KR/sdk.js#xfbml=1&version=v2.10&appId=403216550080274";
  fjs.parentNode.insertBefore(js, fjs);
}(document, 'script', 'facebook-jssdk'));</script>
