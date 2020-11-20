---
layout: post
title:  "How to receive mock api response in the form what I want using a public google spreadsheet document"
slug: "how-to-receive-mock-api-response-in-the-form-what-i-want-using-a-public-google-spreadsheet-document"
date:   2020-11-18 16:00:00 +0900
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

[한국어 게시글은 이 링크 클릭](https://fureweb-com.github.io/blog/2020/11/17/how-to-receive-mock-api-response-in-the-form-i-want-using-a-public-google-spreadsheet-document.html)

![Introduction](/assets/img/posts/20201116-introduction.png)

>First of all, I ask for your understanding that it may not be smooth because it is written with the help of Google Translator because I'm not good at English.

I created a free API using a library called [Public Google Sheets Parser](https://www.npmjs.com/package/public-google-sheets-parser) and wrote a post to share how to use it.

I used AWS Lightsail to create an inexpensive server in the ap-northeast-2 region, where I deployed the [https://api.fureweb.com](https://api.fureweb.com) service with a simple document. If your country is far from South Korea, the API response may be slow.

To use this API, you need to create a Google Spreadsheet document, fill in the header for the first row, data from the second row, and get the Spreadsheet ID. Also, the document's view permission must be set to public.

Spreadsheet ID refers to the value between `https://docs.google.com/spreadsheets/d/` and `/edit#gid=0`.

### Examples of API Calls

Click the sample Google Spreadsheet document link below to see the structure and content of the document.

[https://docs.google.com/spreadsheets/d/1oCgY0UHHRQ95snw7URFpOOL_DQcVG_wydlOoGiTof5E/edit](https://docs.google.com/spreadsheets/d/1oCgY0UHHRQ95snw7URFpOOL_DQcVG_wydlOoGiTof5E/edit)

If you request through curl like the following,
```sh
curl -X GET "https://api.fureweb.com/spreadsheets/1oCgY0UHHRQ95snw7URFpOOL_DQcVG_wydlOoGiTof5E" -H "accept: */*"
```

Then, you can get a response like below.
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

If the spreadsheet ID is invalid or you enter a document ID for which do not have access permission, you will receive an empty array of responses as follows.
```json
{
  "data": []
}
```

Please refer to the simple form written in [API document](https://api.fureweb.com) use before.

By sending an HTTP request, you can get JSON response such as user list, product list, etc., created based on the contents of the spreadsheet you created, with the desired key and value.

If the ID does not exist, or an unauthorized document, the response is always '200 OK'.


### Conclusion

From the client developer's point of view, it can be cumbersome to develop the screen first if the server's API response has not yet been confirmed.

At this time, you can register the actual API response that you expect to receive from the server in Google Spreadsheet and receive the desired JSON response.

There is a big limitation that only public documents can be used, but I think it has its own advantages because API Key is not required. I think it's a good way to use it when it fits in such a case, so I share the content.

I hope it is used well.

Thanks for reading.

<div class="fb-comments" data-href="https://fureweb-com.github.io{{page.url}}" data-width="100%" data-numposts="10"></div>

<div id="fb-root"></div>
<script>(function(d, s, id) {
  var js, fjs = d.getElementsByTagName(s)[0];
  if (d.getElementById(id)) return;
  js = d.createElement(s); js.id = id;
  js.src = "//connect.facebook.net/ko_KR/sdk.js#xfbml=1&version=v2.10&appId=403216550080274";
  fjs.parentNode.insertBefore(js, fjs);
}(document, 'script', 'facebook-jssdk'));</script>
