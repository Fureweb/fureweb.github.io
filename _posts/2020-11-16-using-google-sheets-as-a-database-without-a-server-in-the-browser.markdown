---
layout: post
title:  "브라우저에서 Google Sheets를 서버 없이 데이터베이스처럼 사용하기"
slug: "using-google-sheets-as-a-database-without-a-server-in-the-browser"
date:   2020-11-16 23:57:00 +0900
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

서버 없이 브라우저를 통해 구글 시트의 데이터를 데이터베이스에 저장되어있는 데이터 처럼 활용할 수 있는 방법을 혹시 알고 계셨나요? 매우 간단한 방법이지만 많이 알지 못하고 계시는 것 같아서 공유 해 보려고 합니다.

아주 간단히 동적인 컨텐츠를 제공해야 하는 웹페이지 입장에서 데이터베이스 서버와 웹서버를 구축하여 API로 뽑아내 내려주거나, AWS Lambda같은 서버리스 기능을 사용하는것이 무겁고 어쩌면 사치라고 느껴지는 경우에 요긴할 것 같다고 생각합니다. 또는 프론트엔드 화면을 빨리 만들어야 할 때, 가짜 데이터를 만들기 위한 좋은 대안이 될 수 있지 않을까 생각합니다.

[fetch API를 지원하지 않는 브라우저](https://caniuse.com/fetch)에서는 사용할 수 없다는 점, 문서가 누구나 읽을 수 있는 공개 상태여야한다는 점, 시트를 지정할 수 없이 맨 처음에 위치한 시트의 데이터만 가져올 수 있다는 점이 가장 큰 제약사항이기는 합니다. 하지만 그만큼 API 키가 필요 없고, 간단히 쓰기 편한 것 같다고 느껴 공유할 생각을 하게 되었던 것 같아요.

원래는 그냥 직접 호출해서 가공하는 방법을 풀어 써 보려다가 이 글을 쓰기 위한 [Public Google Sheets Parser](https://www.npmjs.com/package/public-google-sheets-parser)라는 이름의 라이브러리를 간단히 만들어 보았기에, 이것을 어떻게 사용할 수 있는지 설명 해 보겠습니다.


### 데모 페이지 소개 및 사용 방법
[http://fureweb.com/public-google-sheets-parser.html](http://fureweb.com/public-google-sheets-parser.html) 경로에 데모 페이지를 업데이트 해 두었습니다.

사용 해 보는 방법은 매우 간단합니다.

1. [구글 드라이브 홈](https://drive.google.com/drive/u/0/my-drive)으로 이동합니다.
2. 좌측의 새로만들기 버튼 -> Google 스프레드시트 목록을 선택하여 새로운 스프레드시트 입력을 위한 페이지를 띄웁니다.
3. 브라우저의 주소표시줄을 확인 해 보면, `https://docs.google.com/spreadsheets/d/스프레드시트ID/edit#gid=0` 형태로 되어있음을 알 수 있습니다. 여기서 `스프레드시트ID`로 표시해 둔 영역의 범위를 미리 잘 확인 해 주세요. 그리고 우측 상단의 공유 버튼을 눌러 호출되는 팝업 페이지에서 `변경` 버튼을 눌러 `링크가 있는 모든 사용자에게 공개`를 선택한 뒤 완료 버튼을 눌러 공개 상태로 문서를 만들어 준 뒤, 아래와 같이 데이터를 입력 해 보겠습니다.
![스프레드시트 값 입력 예제](/assets/img/posts/20201116-spreadsheet-example.png){:style="width:100%"}
4. 3번에서 말씀드린 `스프레드시트ID`를 정확하게 복사한 뒤, [데모 페이지](http://fureweb.com/public-google-sheets-parser.html)로 이동하여 input창에 붙여넣기 후 GET ITEMS 버튼을 눌러 응답받은 결과를 확인 해 보세요. 아니면 창에 떠 있는 SAMPLE ID로 주어진 값을 복사해서 input창에 붙여넣어 버튼을 눌러보세요. 방금 직접 입력하셨던 머리글과 그 값이 가공되어 배열로 내려오는 것을 확인하실 수 있습니다. 응답이 정상적으로 안내려온다면, 문서 권한에 모두 읽기 권한이 없어서 그럴 가능성이 가장 높습니다.
![샘플 페이지 응답 결과](/assets/img/posts/20201116-sample-page.png){:style="width:100%"}



### 소스 코드
코드가 많이 부끄럽긴 하지만, 관심이 있으시다면 [GitHub에 등록한 소스코드](https://github.com/fureweb-com/public-google-sheets-parser/blob/master/src/index.js)에서 확인하실 수 있습니다.

Google Visualization API에서 내려주는 문자열을 직접 가공해서 필요한 형태의 배열로 만들어 주는게 전부인데, 너무 간단한 내용이라 자세히 설명하기 뭐해서 궁금하시면 소스코드를 확인 해 주시면 될 것 같습니다.

스프레드시트에 form으로 입력받은 내용을 리스트로 뿌려서 보여준다거나, 제목과 상세내용 그리고 이미지 경로 등을 머리글로 만들어 두고 데이터를 넣으면, 간단한 mock용 API로도 활용할 수 있지 않을까 생각합니다.

### Node.js 지원 추가
Node.js에서도 다음과 같이 사용할 수 있습니다.
```js
// public-google-sheets-parser 모듈 설치
$ npm i public-google-sheets-parser

// node.js를 통해 다음의 코드를 실행
const PublicGoogleSheetsParser = require('public-google-sheets-parser')
const spreadsheetId = '10WDbAPAY7Xl5DT36VuMheTPTTpqx9x0C5sDCnh4BGps'
const parser = new PublicGoogleSheetsParser(spreadsheetId)
parser.parse().then((items) => {
  // items는 다음과 같습니다: [{ a: 1, b: 2, c: 3}, { a: 4, b: 5, c: 6 }, { a: 7, b: 8, c: 9 }]
  console.log(items)
})
```

### 마무리
보안에 크게 문제될 부분이 없는 데이터를 서비스하는 정적인 웹 사이트에서 사용하면 특히 괜찮은 방법이지 않을까 생각하고 있습니다.

아직 여러 플랫폼에서 지원하는 형태로 npm 모듈로 만들어 배포하는 방법을 잘 몰라서, CDN을 통한 브라우저에서의 사용과 node.js에서 require로 사용하는 부분밖에 지원하지 못하고 있는 상태입니다. 이 부분과 태스크 러너를 통해 배포용 파일을 만드는 부분을 공부해서 적용할 계획입니다.

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
