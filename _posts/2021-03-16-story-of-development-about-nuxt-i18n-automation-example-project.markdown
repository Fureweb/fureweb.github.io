---
layout: post
title:  "nuxt.js 기반 국제화 처리 자동화 컨셉 및 구현 이야기"
slug: "story-of-development-about-nuxt-i18n-automation-example-project"
date:   2021-03-16 23:40:00 +0900
categories: blog
---
<style>a, li, code { word-break: break-all; }</style>

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

<!-- contents start -->

### TL;DR

국제화 처리를 위한 파일을 직접 작성 없이 구글 시트에 입력된 내용을 기반으로 파일을 생성하고,
나아가 파일이 없어도 국제화 처리를 할 수 있도록 예시를 들어 설명합니다.

### 들어가며

국제화를 고려한 웹 애플리케이션을 작성해야 하는 경우, 화면 내에 등장하는 모든 텍스트가 별도의 국제화 처리기를 통해 표현될 수 있도록 준비해야 합니다.

저는 현재 [nuxt.js](https://nuxtjs.org/)로 웹 앱을 주로 만들고 있고, 국제화 처리를 위해 [nuxt-i18n](https://i18n.nuxtjs.org/) 모듈을 함께 사용하고 있습니다.

이런 환경에서, 국제화 처리를 위해 별도로 key:value 형태로 텍스트를 모아 둔 파일(이하 사전으로 표현)을 다음과 같이 만들어 왔습니다.

```js
// locales/ko/index.js 파일
export default {
  common: {
    title: '제목',
    // ...
  },
  // 다른 속성들
}
```

당연한 얘기이지만 위와 같은 형태로 작업하면, 번역된 텍스트가 변경 될 때마다 해당 텍스트 파일을 수정해야 하고, 그 이후에 별도로 배포를 해야 해당 환경에 적용 됩니다.

이렇게 수동으로 사전 파일을 작성해야 한다는 것은 매우 불편하고, 또 실수하기 쉬운 일이라고 생각했습니다.

그래서 이 파일을 손으로 직접 작성하지 않고, 구글 시트에 저장된 내용을 기반으로 파일이 만들어지도록 하는 컨셉에서, 더 나아가 아예 파일을 만들지 않고 구글 시트의 내용을 바로 끌어다 쓰면 어떨까 하는 생각에서 이 작업을 시작하게 되었습니다.

이를 구현하기 위해, 이전에 만들었던 [public-google-sheets-parser](https://www.npmjs.com/package/public-google-sheets-parser) 라이브러리를 기반으로 한 [nuxt-google-sheets-parser](https://www.npmjs.com/package/nuxt-google-sheets-parser) 모듈을 통해 예시용 웹앱을 한번 작성 해 보려고 합니다.

실제로 얼마나 유용하게 쓰일 수 있을지는 잘 모르겠지만, 최소한 개발 단계에서는 사전용 파일을 만들지 않아도, 원하는 키에 원하는 값들을 편리하게 정의하고 사용할 수 있다는 점이 장점이 될 것이라 생각하며 글 작성을 시작합니다.


### 설계 과정

먼저, [구글 시트 문서](https://docs.google.com/spreadsheets/d/1TZu5G5VxPRoXeCjY7-OQbHFp09y73wGTiDsyzk6UwPQ/edit#gid=0)를 다음과 같은 형태로 준비합니다.

|ko|en|ja|key|key1|key2|key3|key4|...|key10|
|-|-|-|-|-|-|-|-|-|-|
|제목|title|タイトル|**수식**|common|title|
|내용|Contents|内容|**수식**|common|description|
|버튼|button|ボタン|**수식**|common|button|
|문서 경로|Documents path|ドキュメントパス|**수식**|common|sheetsPath|

* [참고용 샘플 문서 링크](https://docs.google.com/spreadsheets/d/1TZu5G5VxPRoXeCjY7-OQbHFp09y73wGTiDsyzk6UwPQ/edit#gid=0)

* 위 문서는 ko 필드에만 한글 텍스트를 넣었고, en, ja 필드 값은 전부 [GOOGLETRANSLATE 함수](https://support.google.com/docs/answer/3093331?hl=ko)를 통해 구글이 번역해준 결과를 사용하도록 해 두었습니다. 실제 번역된 텍스트가 나오기 전 까지 임시로 사용할 수 있을 것이며, 이 부분은 최종적으로 검수를 마친 텍스트로 대체되어야 할 것입니다.

* 입력이 불편해서 시트의 key 열이 key10의 오른쪽에 있었으면 하는 생각이 드신다면, 그렇게 이동하시고 위 함수의 참조 셀들의 값을 변경하시면 됩니다. 또한 key10이 너무 많다면, 불필요한 만큼 지우고 사용하셔도 됩니다.

각각의 **key** 셀에는 다음과 같은 함수를 넣어, key1 ~ key10까지의 값을 dot(.)을 기준으로 병합되여 표현되도록 했습니다. depth가 더 필요한 경우엔 그만큼 추가하거나, 감소할 수 있습니다.

```js
// D2 셀 기준, 그 아래 셀은 이 수식을 복사하여 사용
=CONCATENATE(
  E2,
  IF(F2 <> "", "."&F2, ""),
  IF(G2 <> "", "."&G2, ""),
  IF(H2 <> "", "."&H2, ""),
  IF(I2 <> "", "."&I2, ""),
  IF(J2 <> "", "."&J2, ""),
  IF(K2 <> "", "."&K2, ""),
  IF(L2 <> "", "."&L2, ""),
  IF(M2 <> "", "."&M2, ""),
  IF(N2 <> "", "."&N2, ""),
  IF(O2 <> "", "."&O2, "")
)
```

이렇게 만들어진 key와 locale(ko, en, ja, ...) 셀에 담긴 값들을 기준삼아 Javascript의 Object로는 다음과 같이 표현되기를 기대했습니다.

```js
// key필드에 담긴 값이 'common.title'이고, ko 필드에 담긴 값이 '제목'인 경우
{
  common: {
    title: '제목'
  }
}
```

위 형태로 얻은 결과를 사전으로 사용할 수 있도록, locale/${locale}/index.js 파일을 다음과 같은 형태로 작성했습니다.

```js
// locale/base.js
import set from 'lodash/set'

export default async (context, locale) => {
  // https://docs.google.com/spreadsheets/d/1TZu5G5VxPRoXeCjY7-OQbHFp09y73wGTiDsyzk6UwPQ/edit
  const sheetId = '1TZu5G5VxPRoXeCjY7-OQbHFp09y73wGTiDsyzk6UwPQ'
  const sheetName = 'dictionary'
  const dictionary = {}

  const response = await context.$gsparser.parse(sheetId, sheetName)
  response.forEach((item) => set(dictionary, item.key, item[locale]))

  return Promise.resolve(dictionary)
}

// locale/ko/index.js
import base from '../base'

export default (context) => {
  return base(context, 'ko')
}

// locale/en/index.js
import base from '../base'

export default (context) => {
  return base(context, 'ko')
}

// 그 외 필요한 만큼 생성..
```

이렇게 준비한 뒤, nuxt.config.js 파일의 nuxt-i18n 설정을 다음과 같이 했습니다.

```js
// nuxt.config.js
{
  // ...다른 설정 생략...

  modules: [
    [
      'nuxt-i18n',
      {
        locales: [
          { code: 'ko', iso: 'ko-KR', file: 'ko/index.js' },
          { code: 'en', iso: 'en-US', file: 'en/index.js' },
          { code: 'ja', iso: 'ja-JP', file: 'ja/index.js' },
        ],
        langDir: 'locales/',
        lazy: true,
        defaultLocale: 'ko',
        strategy: 'prefix_and_default',
        vuex: {
          moduleName: 'i18n',
          syncLocale: true,
          syncMessages: true,
          syncRouteParams: true,
        },
      },
    ],
    // 반드시 nuxt-i18n 모듈 등록 이후에 등록되어야 합니다.
    'nuxt-google-sheets-parser',
    // 다른 모듈 생략
  ],

  // ...다른 설정 생략...
}
```

그리고 이렇게 연결된 상태라면, 다음과 같이 template 내에서 사용할 수 있습니다.

```html
<template>
  <div>
    <!-- key가 common.title인 국제화 텍스트가 알맞게 표현됩니다. -->
    <h1>{{ $t('common.title') }}</h1>
  </div>
</template>
```

이처럼 연결시켜 두면, 별도의 파일을 만들지 않고도 국제화 텍스트를 쉽게 표현할 수 있습니다.

하지만 이렇게 두면 구글 시트 API에 장애가 발생했을 때, 사이트의 모든 텍스트가 깨질 수 있다는 치명적인 단점이 있습니다.

이 부분은 구글 시트 파일을 기반으로 사전용 파일을 자동 생성하여 소스코드에 포함되도록 해 주는 방식으로 커버할 수 있을 것이라고 생각했습니다.

그래서 아래와 같은 스크립트를 통해 locale별 fallback.json 파일이 자동으로 생성될 수 있도록 했습니다.

```js
// makeDictionary.js
const fs = require('fs')
const _ = require('lodash')
const PublicGoogleSheetsParser = require('public-google-sheets-parser')

const parser = new PublicGoogleSheetsParser()
const sheetId = '1TZu5G5VxPRoXeCjY7-OQbHFp09y73wGTiDsyzk6UwPQ'
const sheetName = 'dictionary'

const targetLanguages = ['ko', 'en', 'ja']

parser.parse(sheetId, sheetName).then((rows) => {
  const dictionary = {}

  // 언어별 사전을 생성합니다. { [locale]: { key: value, key: value, ... } } 형태가 되도록 합니다.
  rows.forEach((row) => targetLanguages.forEach((lang) => _.set(dictionary, `${lang}.${row.key}`, row[lang])))

  // 필요한 언어들에 대한 fallback 파일을 생성합니다.
  targetLanguages.forEach((lang) => fs.writeFileSync(`./locales/${lang}/fallback.json`, JSON.stringify(dictionary[lang])))

  console.log(`${targetLanguages.length} files created.`)
})
```

이제 필요할 때 마다 위 스크립트를 실행하면, 언어별 fallback 파일이 작성되도록 준비가 되었습니다. 터미널에서 아래 명령을 실행하면, locales 디렉토리에 각각의 fallback 파일이 생성됩니다.

```bash
$ node makeDictionary
# 3 files created.
```

위 부분을 package.json에 넣어서 사용해도 되고, 필요할 때 마다 그냥 호출해서 사용할 수 있을 것입니다.


추가된 fallback 파일의 지원을 위해, 위에 작성했던 base.js파일에 response가 falsy한 경우 fallback 파일을 읽어와서 사전으로 사용하도록 처리를 추가합니다.

```js
import set from 'lodash/set'

export default async (context, locale) => {
  // https://docs.google.com/spreadsheets/d/1TZu5G5VxPRoXeCjY7-OQbHFp09y73wGTiDsyzk6UwPQ/edit
  const sheetId = '1TZu5G5VxPRoXeCjY7-OQbHFp09y73wGTiDsyzk6UwPQ'
  const sheetName = 'dictionary'
  const dictionary = {}

  const response = await context.$gsparser.parse(sheetId, sheetName)
  if (response.length) {
    response.forEach((item) => set(dictionary, item.key, item[locale]))
  } else {
    const { default: fallbackDictionary } = require(`./${locale}/fallback.json`)
    Object.assign(dictionary, fallbackDictionary)
  }

  return Promise.resolve(dictionary)
}

```

이렇게 하면, API로부터 제대로된 응답을 돌려받지 못하게 되는 경우, fallback 파일을 사용할 수 있게 됩니다.

이런 방식으로 국제화를 구현하게 되면 개발 중에는 최대한 편하게 진행할 수 있는 것 같습니다.

만약 사전에 더 이상 추가될 것도, 수정될 것도 없다면, 파일만 가져와서 동작하도록 base.js 파일의 내용을 변경해서 사용할 수 있을 것 같습니다.

### 결론

json파일을 신경쓰지 않고, 공개 권한을 가진 스프레드시트만으로 최대한 간단하게 국제화 처리를 구현할 수 있는 방법을 소개 해 보았습니다.

개발 단계이거나, 아직 안정화가 덜 되어 문서를 수시로 수정해야 하는 경우에 특히 요긴하게 사용할 수 있을 거라고 생각했습니다.

운영 환경에서는 동적으로 가져오지 않고, 등록된 fallback.json 파일만 사용하겠다면 그런 방식으로 설정해서 사용할 수도 있습니다.

이 내용을 간단히 정리하여 [공개 저장소](https://github.com/fureweb-com/nuxt-i18n-automation-example)에 등록 해 두었습니다.

읽어주신 분들에게 조금이라도 도움이 되는 내용이었으면 합니다.

읽어주셔서 감사합니다!

<!-- contents end -->

<div class="fb-comments" data-href="https://fureweb-com.github.io{{page.url}}" data-width="100%" data-numposts="10"></div>

<div id="fb-root"></div>
<script>(function(d, s, id) {
  var js, fjs = d.getElementsByTagName(s)[0];
  if (d.getElementById(id)) return;
  js = d.createElement(s); js.id = id;
  js.src = "//connect.facebook.net/ko_KR/sdk.js#xfbml=1&version=v2.10&appId=403216550080274";
  fjs.parentNode.insertBefore(js, fjs);
}(document, 'script', 'facebook-jssdk'));</script>
