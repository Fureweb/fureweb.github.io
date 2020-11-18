---
layout: post
title:  "공개 구글 스프레드시트 문서를 이용하여 내가 원하는 형태의 mock API 응답받기"
slug: "how-to-receive-mock-api-response-in-the-form-i-want-using-a-public-google-spreadsheet-document"
date:   2020-11-18 05:00:00 +0900
categories: blog
---
<style>a, li { word-break: break-all; }</style>

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

[English contents click here.](https://fureweb-com.github.io/blog/2020/11/17/how-to-receive-mock-api-response-in-the-form-i-want-using-a-public-google-spreadsheet-document.html)

![api.fureweb.com](/assets/img/posts/20201118-api-fureweb-com.png){:style="width:100%"}

안녕하세요. 이 전의 [브라우저에서 Google Sheets를 서버 없이 데이터베이스처럼 사용하기](https://fureweb-com.github.io/blog/2020/11/16/using-google-sheets-as-a-database-without-a-server-in-the-browser.html) 게시글을 작성하기 위해 만들었던 [Public Google Sheets Parser](https://www.npmjs.com/package/public-google-sheets-parser)라는 라이브러리를 이용한 무료 API를 만들어, 사용 방법을 공유하기위해 게시글을 작성하게 되었습니다.

AWS Lightsail을 이용해 가장 저렴한 서버를 생성했고, 그곳에 [https://api.fureweb.com](https://api.fureweb.com) 서비스를 간단한 문서와 함께 배포 했습니다.

구글 스프레드시트 문서를 하나 만든 뒤, 첫번째 행은 머리글, 두번째 행 부터는 데이터를 입력한 뒤 **반드시 공개 보기 설정**을 해 둔 상태에서 스프레드시트 ID만 가져와 사용하시면 편하게 사용하실 수 있을 것 같습니다.

스프레드시트 ID는 https://docs.google.com/spreadsheets/d/ 와 /edit 사이에 있는 값을 의미합니다.

### API 호출 예제

아래의 샘플 구글 스프레드시트 문서 링크를 클릭해서 내용을 확인 해 보세요.
[https://docs.google.com/spreadsheets/d/1oCgY0UHHRQ95snw7URFpOOL_DQcVG_wydlOoGiTof5E/edit](https://docs.google.com/spreadsheets/d/1oCgY0UHHRQ95snw7URFpOOL_DQcVG_wydlOoGiTof5E/edit)

만약 다음과 같이 curl을 통해 요청한다면,
```sh
curl -X GET "https://api.fureweb.com/spreadsheets/1oCgY0UHHRQ95snw7URFpOOL_DQcVG_wydlOoGiTof5E" -H "accept: */*"
```

아래와 같은 응답을 받을 수 있습니다.
```json
{
  "data": [
    {
      "id": 1,
      "title": "This is a title of 1",
      "description": "This is a description of 1",
      "createdAt": "2020-11-12",
      "modifiedAt": "2020-11-18"
    },
    {
      "id": 2,
      "title": "This is a title of 2",
      "description": "This is a description of 2",
      "createdAt": "2020-11-12",
      "modifiedAt": "2020-11-18"
    },
    {
      "id": 3,
      "title": "This is a title of 3",
      "description": "This is a description of 3",
      "createdAt": "2020-11-12",
      "modifiedAt": "2020-11-18"
    },
    {
      "id": 4,
      "title": "This is a title of 4",
      "description": "This is a description of 4",
      "createdAt": "2020-11-12",
      "modifiedAt": "2020-11-18"
    },
    {
      "id": 5,
      "title": "This is a title of 5",
      "description": "This is a description of 5",
      "createdAt": "2020-11-12",
      "modifiedAt": "2020-11-18"
    },
    {
      "id": 6,
      "title": "This is a title of 6",
      "description": "This is a description of 6",
      "createdAt": "2020-11-12",
      "modifiedAt": "2020-11-18"
    },
    {
      "id": 7,
      "title": "This is a title of 7",
      "description": "This is a description of 7",
      "createdAt": "2020-11-12",
      "modifiedAt": "2020-11-18"
    },
    {
      "id": 8,
      "title": "This is a title of 8",
      "description": "This is a description of 8",
      "createdAt": "2020-11-12",
      "modifiedAt": "2020-11-18"
    },
    {
      "id": 9,
      "title": "This is a title of 9",
      "description": "This is a description of 9",
      "createdAt": "2020-11-12",
      "modifiedAt": "2020-11-18"
    },
    {
      "id": 10,
      "title": "This is a title of 10",
      "description": "This is a description of 10",
      "createdAt": "2020-11-12",
      "modifiedAt": "2020-11-18"
    }
  ]
}
```

id가 유효하지 않거나, 접근 권한이 없는 문서 ID를 입력한 경우 다음과 같이 빈 배열의 응답을 받게 됩니다.
```json
{
  "data": []
}
```

[API 문서](https://api.fureweb.com)에 적어 둔 형태로 HTTP 요청을 보내주시면, 본인이 작성해 둔 스프레드시트의 원하는 사용자 리스트, 상품 리스트 등의 데이터를 원하는 key와 value를 가진 JSON Array로 돌려받을 수 있으며, ID가 존재하지 않거나, 권한이 없는 문서여서 실패하는 경우라도 응답은 모두 200 OK로 내려가게 해 두었습니다.


### 마치며

개인적으로는 웹이든 앱이든 클라이언트 입장에서 화면을 개발해야 할 때, 아직 실제 API 응답을 내려받을 수 없을 때 요긴하게 사용할 수 있을 것 같다는 생각을 해서 이렇게 만들어 보게 되었는데요.

기획 롤을 맡은 분이 스프레드시트로 필드명과 값들을 입력 해 둔 상태에서 클라이언트 개발을 맡은 분에게, 특정 스프레드시트 문서에 입력해 둔 내용을 JSON 응답을 받을 수 있으니, 이걸 기반으로 개발 해 주세요~ 하는 식의 요청도 할 수 있지 않을까 생각 해 보았습니다 ㅎㅎ;

얼마나 쓸모가 있을지는 잘 모르겠지만, 클라이언트 개발 시 요긴하게 사용될 수 있었으면 합니다.

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
