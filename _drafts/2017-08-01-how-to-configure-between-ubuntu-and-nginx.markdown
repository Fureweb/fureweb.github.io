---
layout: post
title:  "Ubuntu의 Nginx 서버에 도메인 연결하기"
slug: "how-to-configure-between-ubuntu-and-nginx"
date:   2017-08-01 22:00:00 +0900
categories: blog
---

<div class="fb-like" data-href="https://fureweb-com.github.io{{page.url}}" data-layout="button_count" data-action="like" data-size="small" data-show-faces="true" data-share="true"></div>
<br>

<a href="https://fureweb-com.github.io/blog/2017/07/28/letsencrypt-with-nginx-on-ubuntu.html" target="_blank">Ubuntu-Nginx 서버에서 Let'sEncrypt를 이용한 무료 SSL 적용방법</a> 관련 추가 내용입니다.

웹 브라우저를 통해 특정 도메인<small>(이 포스트에서는 <a href="http://fureweb.com" target="_blank">fureweb.com</a>으로 표현합니다.)</small>에 연결되어있는 자원에 접근하기 위해서는 다음과 같은 준비가 필요합니다.<br>

1. <a href="http://fureweb.com" target="_blank">fureweb.com</a> 이라는 도메인을 구입하여 소유하고 있어야 합니다.  

2. 웹 서버로 사용할 수 있는 네트워크에 상시 연결된 컴퓨터 자원을 보유하고있어야 합니다.  

3. 2번 자원 내에서 실행된, 웹 서버 자신에게 감지될 http 요청을 기다리며 그 요청을 해석한 결과를 응답해줄 수 있는 프로그램이 웹 서버 내에서 실행되고 있어야 합니다.


이번 포스트에서는 Ubuntu라는 운영체제와 Nginx라는 웹서버를 이용하여 본인이 소유한 도메인과 웹 서버를 연결하는 방법에 대해 알아보도록 하겠습니다.


## Ubuntu에서 Nginx 설치











  
<div class="fb-comments" data-href="https://fureweb-com.github.io{{page.url}}" data-width="100%" data-numposts="10"></div>

<div id="fb-root"></div>
<script>(function(d, s, id) {
  var js, fjs = d.getElementsByTagName(s)[0];
  if (d.getElementById(id)) return;
  js = d.createElement(s); js.id = id;
  js.src = "//connect.facebook.net/ko_KR/sdk.js#xfbml=1&version=v2.10&appId=403216550080274";
  fjs.parentNode.insertBefore(js, fjs);
}(document, 'script', 'facebook-jssdk'));</script>