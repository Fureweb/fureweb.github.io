---
layout: post
title:  "우분투에서 nginx 설치부터 무료 SSL 적용까지"
slug: "applying-free-ssl-to-your-ubuntu-server-using-nginx-and-let\'sencrypt-from-scratch"
date:   2017-08-07 15:44:00 +0900
categories: blog
---

<div class="fb-like" data-href="https://fureweb-com.github.io{{page.url}}" data-layout="button_count" data-action="like" data-size="small" data-show-faces="true" data-share="true"></div>
<br>

# 개요
<div style="border-top: 1px solid #eee; margin: 10px 0px"></div>

본 튜토리얼은 다음의 순서대로 진행됩니다.

- 우분투 VPS 생성
- nginx 설치 및 설정
- 도메인 Host IP 설정 및 확인
- Certbot 설치 및 설정
- 최종 확인
- 마치며

windows 환경에서 아래 예제를 실행하셔야 하는 분들은 [mingw](http://www.mingw.org/){:target="_blank"}, [putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html){:target="_blank"}, windows 10 bash 등을 통해 bash shell을 사용하실 수 있습니다. 본 포스트의 내용은 로컬머신에서 bash shell을 사용할 수 있음을 전제하여 작성했습니다.  

또한 vi를 사용할 수 있음을 전제하였으나, vi 사용이 어려우신 경우 nano에 대한 사용 방법을 검색하신 뒤 예제를 따라하시는 것을 추천드립니다. 그런 경우 아래에 표시한 vi 명령을 nano 명령으로 대신하시기 바랍니다.

<br>
# 우분투 VPS 생성
<div style="border-top: 1px solid #eee; margin: 10px 0px"></div>

자신이 사용할 수 있는 클라우드 서비스 내 VPS를 Ubuntu 16.04 LTS 버전으로 생성합니다. 저는 개인적으로 제가 알고있는 한 가장 간단하고 저렴하게 VPS를 생성할 수 있는 [iwinv.kr 클라우드 서비스](//iwinv.kr){:target="_blank"}를 이용하여 VPS를 생성하도록 하겠습니다.  

(사족 : 제가 알고있는 한 가장 저렴한 VPS로 1시간에 4원으로 이용 가능합니다(1 Core, 512MB RAM, 25GB SSD 기준). 현재 포스트를 작성하며 내용대로 실행 해 보고 있는데, 초기 셋업 속도야 비슷한데, 네트워크 inbound 속도가 정말 엄청 느리네요. outbound는 한국 내에서는 쓸만한 것 같은데, inbound 속도가 aws랑 비교하면 정말 심각하게 느린 수준인데.. 제일 저렴한걸 써서 그럴 수도 있다는 생각을 하며 -_-;)

아래 예제에서는 Ubuntu 16.04 LTS(32bit) 버전으로 VPS를 생성하여 예제를 진행하게 됩니다. 편의상 root 계정으로 아래 예제를 수행하겠습니다.

방화벽 설정과 관련된 부분은 이 포스팅의 주제를 벗어나므로, 가능하다면 VPS 생성 시 방화벽은 사용하지 않음으로 설정하셔서 예제를 실습하시기를 희망합니다.

<br>
# nginx 설치 및 설정
<div style="border-top: 1px solid #eee; margin: 10px 0px"></div>

ssh 클라이언트를 이용하여 방금 막 생성한 VPS로 접속합니다. 접속 후 다음의 명령을 차례대로 실행하여 nginx를 서버에 설치한 뒤, 상태를 확인합니다.  

{% highlight bash %}
$ ssh root@your-vps-ip
# 로그인 완료 후 아래 명령을 실행합니다.
$ apt-get update
$ apt-get install nginx
{% endhighlight %}  

설치 후 nginx 실행 상태를 다음과 같이 확인할 수 있습니다.

{% highlight bash %}
$ service nginx status
{% endhighlight %}  

![nginx 설치 후 상태 확인](/assets/img/posts/20170807-nginx-status.png){:width="100%"}  

만약 현재 상태가 inactive라면, 다음의 명령을 통해 nginx 프로세스를 실행시킵니다.
{% highlight bash %}
$ service nginx start
{% endhighlight %}  

설치가 완료된 후 브라우저를 통해 서버의 IP를 입력하여 페이지를 요청하면 welcome to nginx 페이지를 확인할 수 있습니다. 만약 welcome 페이지를 볼수 없다면 방화벽 설정 문제일 가능성이 있습니다. 방화벽과 관련된 부분은 클라우드 서비스 업체마다 설정하는 방법이 다를 수 있으니, 사용하시는 업체의 방화벽 설정 사용 설명을 통해 http 요청을 위한 80번 포트 및 https 요청을 위한 443 포트를 열어둘 수 있도록 합니다.

보유하고 있는 도메인을 현재 VPS로 연결시키기 전, 해당 도메인을 통해 본 서버로 http 요청을 해 오는 경우에 대해 처리할 수 있도록 다음과 같이 nginx 설정을 수행합니다.

{% highlight bash %}
# 다음의 경로로 이동하여 ssltest.fureweb.com 도메인 요청에 대한 설정파일을 생성합니다.
# 아래 경로에 등록된 conf 확장자를 가진 모든 파일이 nginx.conf파일에 의해 include 되며, 
# nginx가 실행될 때 본 디렉토리 내 파일들이 해석되게 됩니다.
$ cd /etc/nginx/conf.d
# 원하는 도메인 명으로 설정 파일을 하나 생성합니다.
$ vi ssltest.fureweb.com.conf
{% endhighlight %}  

{% highlight bash %}
# 다음의 내용을 ssltest.fureweb.com.conf 파일에 입력합니다.
server {
  listen 80;
  server_name ssltest.fureweb.com;
}
{% endhighlight %}  

필요한 설정 파일을 생성하였다면, 다음의 명령을 통해 설정에 오타 등의 오류는 없는지 확인한 뒤 설정을 적용합니다.
{% highlight bash %}
# 설정 파일들에 오류가 없는지 확인한 뒤
$ nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
# 오류가 없다면 설정 파일 내용을 적용합니다.
$ nginx -s reload
{% endhighlight %}  

현재 VPS는 ssltest.fureweb.com에 대한 http 요청을 처리할 수 있는 상태가 되었습니다. 이제 자신이 가지고 있는 도메인 관리업체의 웹사이트에 로그인하여 해당 도메인에 대한 http 요청이 새로 만든 VPS로 전달될 수 있도록 별도의 설정을 진행해 보겠습니다.


<br>
# 도메인 Host IP 설정 및 확인
<div style="border-top: 1px solid #eee; margin: 10px 0px"></div>

본인이 보유하고있는 도메인을 관리하는 업체 사이트에 로그인하여, 자신이 보유한 도메인(또는 서브도메인)에 대한 Host IP를 설정합니다. 

예시로, 제가 보유하고 있는 ssltest.fureweb.com 이라는 서브도메인에 대한 http 요청이 방금 만든 VPS 서버로 연결되도록 설정을 추가하겠습니다. 도메인 등록 대행업체마다 설정하는 방법은 다를 수 있으니, 아래 사진을 참고하셔서 비슷한 메뉴에서 설정을 처리해주세요. 일반적으로 DNS 관리 메뉴의 하위 메뉴에서 설정하실 수 있습니다.

![DNS 관리 메뉴 내 host ip 설정](/assets/img/posts/20170807-set-host-ip.png){:width="100%"}  

만약 이전에 해당 도메인으로의 연결을 한 적이 있다면, 로컬 PC의 DNS캐시를 삭제해야합니다. 없다면 무시하셔도 됩니다.
{% highlight bash %}
# OSX 10.10.4 이상인 경우
$ killall -HUP mDNSResponder

# Windows 10을 사용하는 경우 - powershell 또는 cmd에서
ipconfig /flushdns

# 죄송하지만 이 외에는 검색해 주세요 -_-;;
{% endhighlight %}  

브라우저가 크롬이라면, 주소표시줄에 다음의 경로를 복사&붙여넣기하여 조회되는 화면에서 `Clear Host Cache` 버튼을 클릭하여 캐시를 삭제하세요.  
{% highlight url %}
chrome://net-internals/#dns
{% endhighlight %}  

모든 설정에 문제가 없다면, 이제 웹 브라우저를 통해 내가 가지고 있는 도메인에 대한 연결 요청이 방금 만든 VPS로 전달되게 됩니다. 단, 도메인 관리 업체마다 해당 적용값이 얼마나 빨리 반영되는지는 차이가 있을 수 있으므로, 충분한 시간을 두고 확인을 진행해 주세요. 통상 1~2분 내에는 적용되는 걸로 알고 있으나, 다른 업체는 어떤지 사용해보지 않아서 잘 모르겠네요. ^^;

<br>
# Certbot 설치 및 설정
<div style="border-top: 1px solid #eee; margin: 10px 0px"></div>

먼저 certbot을 설치합니다.
{% highlight bash %}
$ add-apt-repository ppa:certbot/certbot
{% endhighlight %}  

해당 저장소에 담긴 패키지 정보를 확인할 수 있도록 다음과 같이 업데이트를 수행합니다.

{% highlight bash %}
$ apt-get update
{% endhighlight %}

`apt-get`을 이용하여 Certbot을 설치합니다.
{% highlight bash %}
$ apt-get install python-certbot-nginx
{% endhighlight %}

설치된 certbot을 이용하여 ssltest.fureweb.com 도메인에 대한 SSL 인증서만 발급받도록 합니다.

{% highlight bash %}
$ certbot certonly --nginx -d ssltest.fureweb.com
# 위 명령을 실행하면 해당 도메인을 관리하는 사람의 이메일 주소를 입력하라고 나옵니다. 입력해 주세요.
# 만료일에 대한 정보를 보내줍니다.
# 약관에는 동의(A)하셔야 하며,
# EFF 재단과의 이메일을 공유할지 여부를 묻는 부분에 대해서는 원하시는대로 진행하시면 됩니다.
{% endhighlight %}  

아래와 같은 메시지들이 출력됩니다.
![Certbot 설정 과정](/assets/img/posts/20170807-certbot-process.png){:width="100%"}  

위 명령을 수행하고 나면, 아래 경로에 총 5개의 파일이 생성되어 있음을 확인할 수 있습니다. (실제로는 /etc/letsencrypt/archive/도메인명 디렉토리 내 파일에 대한 심볼릭 링크 입니다.)
{% highlight bash %}
$ ls -al /etc/letsencrypt/live/ssltest.fureweb.com
total 12
drwxr-xr-x 2 root root 4096 Aug  7 17:03 ./
drwx------ 4 root root 4096 Aug  7 16:56 ../
lrwxrwxrwx 1 root root   43 Aug  7 17:03 cert.pem -> ../../archive/ssltest.fureweb.com/cert1.pem
lrwxrwxrwx 1 root root   44 Aug  7 17:03 chain.pem -> ../../archive/ssltest.fureweb.com/chain1.pem
lrwxrwxrwx 1 root root   48 Aug  7 17:03 fullchain.pem -> ../../archive/ssltest.fureweb.com/fullchain1.pem
lrwxrwxrwx 1 root root   46 Aug  7 17:03 privkey.pem -> ../../archive/ssltest.fureweb.com/privkey1.pem
-rw-r--r-- 1 root root  543 Aug  7 17:03 README
{% endhighlight %}  

이제 ssltest.fureweb.com 이라는 도메인에 대한 ssl 요청을 처리하기위해 필요한 인증서가 준비된 상태가 되었습니다. nginx가 이 인증서를 통해 https 요청을 처리할 수 있도록, 아까 설정했던 conf 파일을 아래와 같이 수정해 보겠습니다.  

{% highlight bash %}
# 아래 명령어로 기존 설정파일 편집
$ vi /etc/nginx/conf.d/ssltest.fureweb.com.conf

# 다음과 같은 형태가 되어야합니다.
server {
    # http://ssltest.fureweb.com 요청에 대해
    # https://ssltest.fureweb.com 으로 리다이렉트 처리합니다. 
    listen 80;
    server_name ssltest.fureweb.com;
    return 301 https://ssltest.fureweb.com;
}

server {
    # https://ssltest.fureweb.com 으로의 요청을 리스닝합니다.
    listen 443;
    server_name ssltest.fureweb.com;

    ssl on;
    ssl_certificate /etc/letsencrypt/live/ssltest.fureweb.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/ssltest.fureweb.com/privkey.pem;

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers HIGH:!aNULL:!MD5;

    # 예제에서는 이 외에 별도 설정을 추가하지 않고, SSL 요청이 정상적으로 처리되는지만 확인합니다.
    # 일반적으로 아래에는 현재 도메인과 연결된 document_root 등에 대한 설정 기술하거나
    # proxy 설정을 통해 동적 처리가 가능한 다른 WAS와 연동하여 응답을 핸들링합니다.
}
{% endhighlight %}  

위 설정을 저장한 뒤, conf 파일에 오타는 없는지 확인하고 문제가 없다면 설정 내용을 적용시킵니다.

{% highlight bash %}
# 설정 파일들에 오류가 없는지 확인한 뒤
$ nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
# 오류가 없다면 설정 파일 내용을 적용합니다.
$ nginx -s reload
{% endhighlight %}  


<br>
# 최종 확인
<div style="border-top: 1px solid #eee; margin: 10px 0px"></div>

브라우저의 주소창에 https://ssltest.fureweb.com를 입력하여 연결이 제대로 이뤄지는지 확인합니다. 브라우저 캐시로 인해 https로 리다이렉트 처리가 되지 않은 페이지를 보여줄 수 있으니, 크롬 기준으로 강력 새로고침을 실행시켜주세요.

브라우저 주소표시줄 좌측에 자물쇠가 활성화 된 모습을 확인하실 수 있게 됩니다.  
<small><del>주소가 ssltest2.fureweb.com인건 무시해주세요... -_-;</del></small>

![SSL 적용 완료](/assets/img/posts/20170807-ssl-connection.png){:width="100%"}  


<br>
# 마치며
<div style="border-top: 1px solid #eee; margin: 10px 0px"></div>

우분투 서버를 생성하는 것 부터 Nginx 설치 및 설정, 그리고 SSL 적용하기까지 모든 과정에 대해 확인해 보았습니다. 혹시라도 본 포스트를 보고 막히는 부분이 있거나 설명이 부족한 부분이 있다면 언제든지 댓글이나 질문 부탁 드리겠습니다.  

읽어주셔서 감사합니다!


<div class="fb-comments" data-href="https://fureweb-com.github.io{{page.url}}" data-width="100%" data-numposts="10"></div>

<div id="fb-root"></div>
<script>(function(d, s, id) {
  var js, fjs = d.getElementsByTagName(s)[0];
  if (d.getElementById(id)) return;
  js = d.createElement(s); js.id = id;
  js.src = "//connect.facebook.net/ko_KR/sdk.js#xfbml=1&version=v2.10&appId=403216550080274";
  fjs.parentNode.insertBefore(js, fjs);
}(document, 'script', 'facebook-jssdk'));</script>