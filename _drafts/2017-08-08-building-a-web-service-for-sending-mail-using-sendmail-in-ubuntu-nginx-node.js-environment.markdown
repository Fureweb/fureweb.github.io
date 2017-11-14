---
layout: post
title:  "Ubuntu-NginX-Node.js 환경에서 sendmail을 이용한 메일발송 웹 서비스 구축하기"
slug: "Building-a-web-service-for-sending-mail-using-sendmail-in-Ubuntu-Nginx-node.js-environment"
date:   2017-08-08 20:00:00 +0900
categories: blog
---

<div class="fb-like" data-href="https://fureweb-com.github.io{{page.url}}" data-layout="button_count" data-action="like" data-size="small" data-show-faces="true" data-share="true"></div>
<br>

# 경로
https://developers.fureweb.com

위 경로에서 접근, 각 서비스 별 api 신청


# 개요  
개인적으로 웹 애플리케이션을 만들면서, 웹에서 메일을 발송할 때 대부분 gmail의 smtp서버를 이용해 대신 발송 요청을 하거나, php에서의 mail 함수를 이용하여 간단하게 발송하거나, conoha 등의 클라우드 서비스에서 제공하는  하는 부분에 대해서만 사용해 본 경험을 가지고 있었던 부분에 대해 불편함과 궁금증을 늘 가지고 있었던 상황이었습니다.  
1년 반 정도의 기간 동안 웹 프로그래머로서 월급 받으며 일하면서 어느정도 감각을 익히기도 했고, 지금까지 깨작깨작 쌓인 지식들을 조합해 보면 이제는 메일서버 구축부터 발송까지 어느정도는 처리할 수 있겠지 라는 생각이 들게 되었습니다.  
하지만 nodemailer나, smpt-server라는 npm 모듈 없이는 아직 혼자의 힘으로 모든걸 처리할 수는 없으므로 제대로된 처리라는 생각은 들지 않는건 사실입니다. 아직 바퀴를 새로 만들기에는 내공이 부족하니 현재 상황에서는 이정도로 만족한다는 마음으로 본 포스팅을 작성하기 시작합니다.

# 서비스 컨셉  
클라이언트로부터 받은 http 요청을 통해, 발급받은 KEY가 유효한 경우에만 메일발송을 대신 해 주는 서비스를 만들려고 합니다.  

기본적으로 구조는 매우 간단하게 작성하려 합니다.  

어느곳이든 메일을 발송하고 싶어하는 사람의 웹 사이트에서, https://mailer.fureweb.com/api/mail 이 경로로 유효한 http 요청을 보내는 경우 메일 발송을 처리한 뒤 선택적으로 응답하도록 만들고 싶습니다. 만약 동기적으로 처리되어야 한다면, 요청 시 request body의 JSON 내 isSync라는 키에 true라는 값을 넣어주면 동기적으로 처리되며, 생략 시에는 기본적으로 비동기로 처리되도록 할 것입니다. 다만, 비동기로 처리하는 경우에는 무조건 응답이 `200 OK`만 전달될 것이므로, 응답이 정확하게 필요한 경우에만 isSync를 설정하여 사용하도록 하겠습니다.  

메일 내 파일 첨부에 대한 부분은 이곳에서 다루지 않겠습니다. 만약 본문 내 파일이 첨부되어야 한다면, 해당 파일 다운로드 링크를 메일 본문 하단에 HTML로 작성하여 넣는 형태로 작성이 되어야 할 것입니다.

# 서비스 아키텍쳐

![메일 발송 처리를 위한 서비스 아키텍쳐](/assets/img/posts/mailer-fureweb-architecture.png){:width="100%"}  

http 또는 https 요청을 Nginx에서 감지하게 되면, Node.js를 통해 nodemailer / smtp-server 기반으로 작성된 메일 발송 프로그램이 해당 요청에 대한 메일을 대신 작성하여 발송시켜 줍니다.  

Node.js에서는 요청에 담긴 API 키가 유효한지 데이터베이스에 쿼리를 전송하여 결과를 확인하여 유효한 경우에만 메일을 발송해주고, 유효하지 않은 경우 code에는 406, message에는 invalid api key 라는 메시지를 담아 응답하며 요청에 대한 처리를 마치도록 설계할 것입니다.

<del>### 원뿔 모양 바꾸기!! 윈도 그림판으로라도!!</del>


# 단계별 구현 방법

1. Nginx 설정

2. Node.js 프로그램 작성
- 의존성 1) nodemailer
- 의존성 2) smtp-server

3. 데이터베이스 생성
- MariaDB DDL

# 데이터 프로토콜 설계

http 요청 시 다음과 같은 형태로 데이터를 전송하도록 하겠습니다.

- request line  
{% highlight curl %}
POST http://api.fureweb.com/mail HTTP/1.1 
{% endhighlight %}  

- request body  
{% highlight json %}
{
  "isSync" : true,
  "key" : "API_KEY",
  "from" : "보내는사람이메일주소",
  "to" : ["필요한만큼의받는사람이메일주소"],
  "cc" : ["필요한만큼의참조자이메일주소"],
  "title" : "메일 제목",
  "content" : "메일 본문"
}
{% endhighlight %}  

Node.js를 통해 전달받은 http 요청 처리에 대한 응답은 다음과 같은 형태로 전송하도록 하겠습니다.  
code에는 요청에 대한 응답 코드를, message에는 code에 대한 메시지를 담도록 합니다.  
- response body
{% highlight json %}
{
  "code" : 200,
  "message" : "OK"
}
{% endhighlight %}  

# 마무리  

현재 [https://mailer.fureweb.com](https://mailer.fureweb.com){:target="_blank"} 이 경로에서 무료 메일 발송 서비스를 운영하고 있습니다. 일 또는 월 발송 제한량은 없습니다. 서버가 터질 때 까지 무제한(\-\_\-)으로 사용하실 수 있게 할 계획입니다. 가입 및 사용 방법은 간단합니다.  

1. 위 경로 접속
2. 메일 주소로 가입
3. API KEY 발급신청
4. 해당 KEY를 이용해 메일을 발송할 도메인 등록 (무제한)
5. 4번을 통해 등록된 도메인에 대한 화이트도메인 설정 적용
6. 즉시 사용


<div class="fb-comments" data-href="https://fureweb-com.github.io{{page.url}}" data-width="100%" data-numposts="10"></div>

<div id="fb-root"></div>
<script>(function(d, s, id) {
  var js, fjs = d.getElementsByTagName(s)[0];
  if (d.getElementById(id)) return;
  js = d.createElement(s); js.id = id;
  js.src = "//connect.facebook.net/ko_KR/sdk.js#xfbml=1&version=v2.10&appId=403216550080274";
  fjs.parentNode.insertBefore(js, fjs);
}(document, 'script', 'facebook-jssdk'));</script>