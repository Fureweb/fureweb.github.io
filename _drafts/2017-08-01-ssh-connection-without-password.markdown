---
layout: post
title:  "비밀번호 없이 원격서버로 ssh 접속하기"
slug: "ssh-connection-without-password"
date:   2017-08-01 20:00:00 +0900
categories: blog
---

<div class="fb-like" data-href="https://fureweb-com.github.io{{page.url}}" data-layout="button_count" data-action="like" data-size="small" data-show-faces="true" data-share="true"></div>
<br>

세 줄 요약부터 하고 시작하겠습니다.

- 로컬 PC에서 ssh-keygen 명령을 통해 로컬 PC의 개인키와 공개키를 발급한다.  

- 발급한 로컬 PC의 공개키를 원격 서버에 비밀번호 없이 접속하려하는 계정의 홈 디렉토리 내부 .ssh 디렉토리 내 authorized_keys 파일 내에 기록한다.   
> root 계정인 경우 다음 경로의 파일일 것입니다. -> /root/.ssh/authorized_keys

- 로컬 PC에서 원격 서버로 ssh 접속을 시도하면, 해당 계정으로의 로그인은 더이상 비밀번호를 요구하지 않게 된다.

---

이제 각각의 단계가 의미하는 바에 대해서 확인해 보도록 하겠습니다.


1. ssh-keygen 명령을 통해 로컬 PC에서 개인키와 공개키를 발급한다.

ssh-keygen이란, 



  
  
<div class="fb-comments" data-href="https://fureweb-com.github.io{{page.url}}" data-width="100%" data-numposts="10"></div>

<div id="fb-root"></div>
<script>(function(d, s, id) {
  var js, fjs = d.getElementsByTagName(s)[0];
  if (d.getElementById(id)) return;
  js = d.createElement(s); js.id = id;
  js.src = "//connect.facebook.net/ko_KR/sdk.js#xfbml=1&version=v2.10&appId=403216550080274";
  fjs.parentNode.insertBefore(js, fjs);
}(document, 'script', 'facebook-jssdk'));</script>