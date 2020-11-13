---
layout: post
title:  "브라우저에서 자식의 너비에 따라 부모가 말줄임표 처리 되었는지 확인하기"
slug: "how-to-check-parent-has-been-ellipsized-by-child-s-width"
date:   2020-11-13 23:30:00 +0900
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

# 브라우저에서 자식의 너비에 따라 부모가 말줄임표 처리 되었는지 확인하기

### 서론
아래 얘기들은 어쩌면 너무 당연한 얘기일 수도 있는데, 처리해 본 적이 없으면 삽질을 많이 하게 되는 경우가 있습니다.

저 같은 경우는 특정 HTML 요소의 높이가 변경될 가능성이 없는 경우에는 요구사항에 적혀있지 않더라도 기본적으로 말줄임표 처리가 될 수 있도록 작업을 하고 있습니다.

부모 요소가 자식들 보다 더 좁은 너비를 가져 모든 자식들이 표현되기 어려운 상황이라면, 스크롤 바를 두어 모든 자식들이 스크롤을 통해 보일 수 있도록 기회를 주거나 아니면 말줄임표 처리가 되도록 많이 하고 있을텐데요.

스크롤 바를 제공한다면 모든 자식들이 사용자에게 노출 될 기회가 있을것이고, 말줄임표 처리를 하게 된다면 앞선 자식들만 노출되고 나머지 자식들은 부모가 충분한 너비를 가지지 못한다면 영원히 사람들에게 잊혀진 존재가 될 것입니다.

이처럼 말줄임표 처리도 포기할 수 없고, 숨겨진 자식들도 빛을 봐 주게 해야한다면 어떻게 해야할까요?

### 말줄임표 적용하기
제목처럼, 자식의 너비를 확인 해 봤더니 부모의 너비보다 더 넓었다는게 확인되었다면, 이 때 마우스를 부모 위에 올렸을 때 자식들이 모두 표현될 수 있도록 툴팁을 바로 아래 표현을 하거나 하는 방법으로 목표를 달성할 수 있을 것 같습니다.

저는 이런 상황에서 아래와 같은 방법으로 문제를 해결했습니다.

우선, 말줄임표 처리를 위해서는 부모에 다음과 같은 css 속성을 가지게 해 주면 됩니다.

```html
<style>
.parent {
  width: 130px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
</style>

<div class="parent">
  <!-- 아래 label은 16px 기준으로 200px 정도의 너비를 가집니다. --> 
  <label>이건 뭐 하는 새끼손가락일까요?</label>
</div>
```

위와 같은 상황이라면, 부모는 130px이고 자식은 200px이 되기때문에 자식이 가진 모든 텍스트를 보여주지 못하고

![이건 뭐 하는 새끼...](/assets/img/posts/20201113-what-the.png){:style="width:100%"}

라는 욕설로 변하게 될 수가 있습니다. -_-;

아무튼 다음의 css 속성들을 이용하여 말줄임표 처리를 할 수 있습니다.
|속성|값|설명|
|:---|:---|:---|
|white-space|nowrap|부모가 가진 width를 초과하는 자식의 텍스트가 있더라도, 부모가 주어진 높이 내에서 계속 옆으로 이어 작성될 수 있도록 해 주는 설정|
|overflow|hidden|부모가 가진 영역 바깥에 자식이 표현되지 않도록 해 주는 설정|
|text-overflow|ellipsis|위 설정을 통해 자식의 너비가 부모 너비를 초과한 경우에 적절하게 말줄임표 처리를 하도록 하는 설정|


이제 다음과 같은 HTML 코드가 있다고 해 보겠습니다.
```html
<style>
.text-container {
  font-size: 40px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
</style>

<div class="text-container">
  <label class="text">난 text-container의 너비에 따라 말줄임표 처리가 될 수 있어!</label>
</div>
```

위와 같이 코드를 작성한 상황에서, text class에 담긴 문자의 길이가 길어져 브라우저에서 말줄임표 처리가 되었음을 알기 위해선 어떤 방법이 있을까요?

처음엔 HTMLElement에서 overflowed된 상태를 혹시 제공해주는건 아닐까 찾아보았으나.. 존재하는데 제가 잘못알고 있는 것일 수도 있지만, 그런건 존재하지 않았습니다.

그래서 결국 text를 감싸고 있는 text-container의 너비와, 그 안의 text의 너비를 비교해 본 뒤, text가 text-container보다 더 너비가 넓은지를 비교해 보는 방법으로 말줄임표 처리가 되었는지 확인 해 보았습니다.

```js
const textContainerElement = document.querySelector('.text-container')
const textElement = document.querySelector('.text')

if (textElement.offsetWidth > textContainerElement.offsetWidth) {
  // 말줄임표 처리 된 상태
}
```

### 자식의 수와 총 너비가 항상 변하는 경우를 대비해야할 때
하지만 실제 환경에서는 이처럼 고정된 너비들을 가진 요소들만 제어할 리가 없겠죠? 저는 다음과 같은 시나리오를 어떻게 처리해야하는지 고민 해 본 적이 있었습니다.

```scss
.flexible-options {
  height: 40px;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
  .option {
    display: inline-block;
    border: 1px solid black;
  }
}
```

```html
<dd class="flexible-options">
  <dl class="option">동적으로 변하는 옵션 1</dl>
  <dl class="option">동적으로 변하는 옵션 2</dl>
  <dl class="option">동적으로 변하는 옵션 3</dl>
  <dl class="option">...</dl>
</dd>
```

위와 같은 상황에서는, 각 option이 차지하는 너비에 따라 variable-options가 말줄임표 처리가 될 수도, 아닐 수도 있게 됩니다.

결국 flexible-options 내에 children들이 존재하는 상태에서 children의 offsetWidth 값을 모두 더해 봐야만 말줄임표 처리가 되었는지 확인할 수 있습니다.

```js
const flexibleOptionsElement = document.querySelector('.flexible-options')

const parentWidth = flexibleOptionsElement.offsetWidth
const childrenWidth = [...flexibleOptionsElement.children]
  .map(({ offsetWidth }) => offsetWidth)
  .reduce((p, c) => p + c, 0)

if (childrenWidth > parentWidth) {
  // 말줄임표 처리 된 상태
}
```

윈도우 resize 또는 option의 텍스트가 변경 될 때 마다 위 요소들의 offsetWidth를 확인해서 말줄임표 처리가 되었는지 확인할 수 있게 됩니다.

만약 최신 상태의 자식의 너비가 부모 너비보다 더 넓은 경우, 해당 parent에 마우스를 올렸을 때 툴팁을 표현하여 모든 요소가 보일 수 있도록 작업을 해 주는 형태 또는 부모의 높이/너비 등을 충분히 넓혀주는 식으로 처리하게 된다면 자식이 더 넓어서 화면에 보여지지 않을 수 있는 상황에서 모두 노출시킬 수 있게 됩니다.

<div class="fb-comments" data-href="https://fureweb-com.github.io{{page.url}}" data-width="100%" data-numposts="10"></div>

<div id="fb-root"></div>
<script>(function(d, s, id) {
  var js, fjs = d.getElementsByTagName(s)[0];
  if (d.getElementById(id)) return;
  js = d.createElement(s); js.id = id;
  js.src = "//connect.facebook.net/ko_KR/sdk.js#xfbml=1&version=v2.10&appId=403216550080274";
  fjs.parentNode.insertBefore(js, fjs);
}(document, 'script', 'facebook-jssdk'));</script>
