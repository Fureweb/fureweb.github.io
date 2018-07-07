---
layout: post
title:  "2018년 5월 PWA 기술 리서치"
slug: "may-16-2018-pwa-technical-research"
date:   2018-05-16 12:39:00 +0900
categories: blog
---
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

# PWA 개념

**PWA**는 Application Shell(이하 App Shell), Web App Manifest, Service Worker 세가지 주요 구성요소로 이루어져있습니다.

![참고 이미지 링크](https://xamarinhelp.com/wp-content/uploads/2018/02/PWAArchitecture.png)

**App Shell**은 PWA를 작성하는 방법 중 하나로, UI를 구성하기위한 최소한의 HTML + CSS + Javascript로 구성되어있습니다. 이 App Shell은 한 번 방문하면 캐싱되기때문에 재방문 하는 경우 App Shell 자원은 로딩하지 않고 필요한 컨텐츠만 네트워크를 통해 로딩해서 사용합니다. 즉, 이렇게 캐싱된 데이터는 오프라인에서도 사용할 수 있다는 것을 의미합니다.

App Shell에 대한 추가 정보는 [이쪽](https://developers.google.com/web/fundamentals/architecture/app-shell?hl=ko) 링크에서 확인해 주세요.

**Web App Manifest**는 사용자가 앱을 볼 것으로 예상되는 영역(예: 휴대기기 홈 화면)에 웹 앱이나 사이트를 나타내는 방식을 개발자가 제어하고, 사용자가 시작할 수 있는 항목을 지시하고, 시작 시의 모습을 정의할 수 있는 JSON 파일입니다. 또한 사이트 북마크를 기기의 홈 화면에 저장하는 기능 등을 제공합니다.

Web App Manifest에 대한 추가 정보는 [이쪽](https://developers.google.com/web/fundamentals/web-app-manifest/?hl=ko) 링크에서 확인해 주세요.

**Service Worker**란 웹페이지와는 별개로 작동하는 자바스크립트 코드를 백그라운드에서 실행할 수 있도록 도와줍니다. 이것과 함께 HTML5 API인 CacheStorage를 사용하여 백그라운드 네트워크를 통해 읽어야 하는 추가적인 데이터를 읽어온 뒤 CacheStorage에 저장하여 서비스를 제공하도록 앱을 작성한다면 더 나은 성능을 사용자에게 보여줄 수 있을 것입니다.

Service Worker에 대한 추가 정보는 [이쪽](https://developers.google.com/web/fundamentals/primers/service-workers/?hl=ko) 그리고 브라우저별 지원 현황은 [이쪽](https://jakearchibald.github.io/isserviceworkerready/) 링크에서 확인할 수 있습니다.

아래 설명글에도 나오는 이야기이지만, 실행한지 3초 이내에 로딩이 완료되지 않으면 53%의 사용자가 이탈한다는 구글의 조사 결과가 있었습니다. 이러한 사용자의 이탈을 막기 위해 PWA를 적용하게 되면 이런 이유로 이탈하는 사용자들은 막을 수 있을 것입니다.

<br>

# 2018년 2월에 조사된 프로그래시브 웹 앱 현황

[이쪽](https://medium.com/progressive-web-apps/2018-state-of-progressive-web-apps-f7517d43ba70) 링크(영문)의 블로그 게시글을 확인해 주세요.


<br>

# PWA를 통한 웹 푸시 알림 지원 현황

현재 (2018년 5월 16일) 기준으로 확인된 웹 푸시 알림 지원 브라우저는 다음과 같습니다.

*   데스크탑 & 안드로이드 크롬 42+
*   맥OSX 사파리 7.1+
*   안드로이드 파이어폭스 48+
*   데스크탑 파이어폭스 44+
*   iOS는 어떤 브라우저도 현재 지원하지 않음

웹 푸시 알림을 제외한 다른 기능들은 iOS에서 일부 지원하고 있는 것으로 보이며, 관련하여 정리된 내용은 [이곳](https://www.monterail.com/blog/pwa-for-apple-ios) 링크를 통해 확인할 수 있습니다.

<br>

# 웹 푸시 기능 테스트용 PWA 리포트

### 개요

개인 서버에 웹 푸시 테스트용 PWA를 작성해 두었습니다. 테스트 가능한 환경에서 아래 PWA 경로로 접속한 뒤 알림 권한을 승인한 뒤, 화면 내 버튼을 이용하여 푸시 요청을 보낼 수 있고, 요청 후 브라우저를 백그라운드로 내린 뒤 기다리면 푸시메시지를 받을 수 있도록하는 간단한 웹 앱을 제작하였습니다.

원활한 기능 확인을 위해 **안드로이드 또는 데스크탑 크롬 브라우저에서 테스트 해 보시는 것을 추천**합니다.

### 테스트 완료 환경 (2018년 5월 10일 기준)

##### 안드로이드

*   크롬 웹 브라우저
*   브레이브 브라우저

##### macOS

*   크롬 웹 브라우저
*   파이어폭스 브라우저

##### iOS

*   미지원

### PWA 경로

[https://push.fureweb.com](https://push.fureweb.com/)

### 개발환경

*   Ubuntu, Nginx, PHP
*   Let’s encrypt (https certificate)
*   HTML, Javascript
*   Google Firebase

### 앱 구조

*   index.php : PWA 메인 페이지입니다.
*   manifest.json : PWA를 위해 필요한 필수 설정파일입니다.
*   firebase-messaging-sw.js : 메인 페이지에서 알림 허용을 누르면 설치되는 서비스워커쪽 코드입니다.
*   /scripts/main.js : 사용자에게 알림 권한 요청을 하고, 파이어베이스 토큰을 취득하며, 푸시메시지 발송 요청 처리 등을 하는 자바스크립트 코드입니다.
*   /api/push.php : index.php 페이지에서 발급받은 토큰으로 정해진 테스트용 푸시메시지를 발송합니다.

### 테스트 시나리오

*   테스트 완료 환경에 포함되는 기기의 브라우저에서 [https://push.fureweb.com](https://push.fureweb.com/) 경로에 접속
*   알림허용 클릭
*   웹 앱 좌측 하단의 ‘푸시 테스트’ 버튼 클릭
*   팝업 얼럿창의 확인 클릭
*   홈버튼을 눌러 브라우저를 백그라운드로 보낸 뒤 10초 대기
*   10초 후 사용중인 기기로 전달된 알림메시지 확인

### 이슈사항 (2018년 5월 10일 기준)

*   현재 iOS의 모든 브라우저에서 동작하지 않습니다. (iOS 크롬 브라우저도 사파리와 같은 웹킷 엔진 사용 중으로, 사파리에서 지원되지 않는 기능은 지원하지 않는것으로 알고있습니다.)
*   안드로이드 웹뷰 및 삼성인터넷 브라우저로는 웹 푸시 기능이 정상 작동하지 않습니다.
*   안드로이드 기기에서 크롬 앱에대한 알림이 해제되어있는 경우, 브라우저 내에서 알림 승인했다해도 실제 푸시 메시지가 수신되지 않습니다.
*   안드로이드 절전모드에서는 백그라운드 네트워크를 사용하지 않게 되어있어, 실제 푸시 메시지가 수신되지 않습니다.


<br>

# 구글이 설명하는 PWA

이 챕터의 내용은 [이쪽](https://developers.google.com/web/progressive-web-apps/)의 게시글을 번역하였습니다.

### Progressive Web Apps

프로그래시브 웹 앱이란 웹에서 놀라운 사용자 경험을 제공하는 새로운 방법입니다. 웹에 도달할 수 있는 사용자 경험이며 다음의 특성을 가지고 있습니다.

*   신뢰가능한 : 불확실한 네트워크 상태에서도 즉시 로딩되며 크롬 브라우저가 오프라인일때 노출되는 공룡이 절대 노출되지 않습니다.
*   빠른 : 이상한 스크롤(janky scroll)이 없는 엄청 부드러운 애니메이션과 UI로 신속하게 응답합니다.
*   매력적인 : 몰입된 사용자 경험으로 앱 처럼 느낄 수 있습니다.

이러한 새로운 수준의 품질 덕분에 사용자의 홈 스크린에 설치될 수 있습니다.

### 신뢰가능한

사용자의 홈 스크린에서 앱을 실행하면 네트워크의 상태와 관계 없이 서비스 워커가 PWA를 즉시 로드할 수 있게 합니다. 자바스크립트로 작성된 서비스워커는 클라이언트측의 프록시와 비슷하며 캐시를 제어하고 리소스 요청에 응답하는 방법을 제공합니다. 주요 리소스를 사전에 캐시하여 네트워크에 대한 의존성을 없애고 사용자에게 즉각적이고 안정적인 환경을 제공합니다.

### 빠른

화면 로딩에 3초 이상이 걸리면 53%의 사용자가 사이트 접속을 포기합니다. 그리고 일단 로드되면, 사용자들은 속도가 빨라지기를 기대합니다. PWA는 이상한 스크롤링이나 응답속도가 느린 인터페이스가 없습니다.

### 매력적인

PWA는 앱스토어를 통하지 않아도 설치가 가능하며, 사용자의 홈 스크린에 추가될 수 있습니다. 또한 웹 앱 매니페스트 파일을 통해 몰입가능한 풀스크린 환경을 제공하며, 사용자가 앱에 다시 들어올 수 있도록 웹 푸시 알림을 사용할 수 있습니다.

웹 앱 매니페스트 파일을 사용하면 앱이 화면에 어떻게 나타날지, 어떻게 실행될지를 제어할 수 있습니다. 홈 스크린 아이콘이나 앱이 실행될 때 읽어들일 페이지, 화면의 방향 그리고 브라우저를 표시할지 하지 않을지 등을 설정할 수 있습니다.

### 프로그래시브 웹 앱을 만드는 이유는 무엇인가요?

고품질의 PWA를 구축하면 사용자를 즐겁게 하기 쉽고, 참여도와 전환율을 높일 수 있는 놀라운 이점이 있습니다.

- **홈스크린에 설치하기 적합**

PWA로의 등록 조건에 부합된다면, 크롬 프롬프트가 PWA를 홈스크린에 추가할 것인지 묻는 프롬프트를 사용자에게 보여줍니다.

- **증가된 참여**

eXtra Electronics 사이트에서는 웹 푸시 알림기능을 통해 4배의 참여율을 증가시킬 수 있었습니다. 또한 이 사용자들은 다른 사용자들에 비해 4배 더 많은 시간동안 사이트에 머물렀습니다.

- **네트워크 조건에 관계없는 안정된 작업**

Konga라는 PWA에서는 초기 페이지 로드를 위한 데이터 전송의 63% 감소, 첫 거래까지의 데이터 사용량의 84% 감소를 이룰 수 있었는데 이는 서비스워커를 통해 달성할 수 있었습니다.

- **향상된 전환율**

놀라운 사용자 경험을 전달할 수 있는 PWA 능력을 통해 알리익스프레스에서의 모든 브라우저의 신규 사용자 전환율을 104% 향상시켰으며, iOS에서는 82% 향상시켰습니다.

### Lighthouse

Lighthouse라는 오픈소스의 프로그래시브 웹 앱의 품질을 향상시키기위한 자동화된 도구는 이전에 수동으로 수행했던 대부분의 테스트를 제거합니다. 연속적인 통합 시스템에서도 Lighthouse를 활용하여 회귀 테스트를 수행할 수 있습니다.


<br>

# 마치며


PWA로 웹 앱을 작성하면 마치 앱인 것 처럼 전체화면을 사용할 수 있으며, 홈 스크린에도 추가할 수 있고, 앱 쉘의 캐싱을 통해 오프라인에서도 동작할 수 있게 할 수 있는 장점, 그리고 서비스 워커를 활용하여 필요한 자원이나 데이터를 백그라운드 네트워크를 통해서 얻어올 수 있음을 확인하게 됐습니다.

다만 모든 브라우저들이 PWA의 모든 기능을 지원하려면 아직 상당히 많은 시간이 필요하겠지만, 웹 푸시 알림 기능을 통해 사용자의 재방문을 쉽게 유도할 수 있는 기능을 머지않아 활용할 수 있다는 점을 확인할 수 있었습니다.


<div class="fb-comments" data-href="https://fureweb-com.github.io{{page.url}}" data-width="100%" data-numposts="10"></div>

<div id="fb-root"></div>
<script>(function(d, s, id) {
  var js, fjs = d.getElementsByTagName(s)[0];
  if (d.getElementById(id)) return;
  js = d.createElement(s); js.id = id;
  js.src = "//connect.facebook.net/ko_KR/sdk.js#xfbml=1&version=v2.10&appId=403216550080274";
  fjs.parentNode.insertBefore(js, fjs);
}(document, 'script', 'facebook-jssdk'));</script>