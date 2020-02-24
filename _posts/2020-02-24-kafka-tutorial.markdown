---
layout: post
title:  "Kafka 시작하기"
slug: "kafka-tutorial"
date:   2020-02-24 00:30:00 +0900
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

<style>
h2 { color: #fff !important; margin-top: 4rem !important; margin-bottom: 2rem !important; padding: 0.8rem; background-color: #8b9dc3 !important; border-bottom: 5px solid #3b5998; border-right: 2px solid #3b5998; }
</style>

# Kafka 시작하기

## Quick start
#### 소스 다운로드
```bash
wget http://apache.tt.co.kr/kafka/2.4.0/kafka_2.12-2.4.0.tgz
```

#### 압축 해제 및 디렉토리 이동
```bash
tar -xzf kafka_2.12-2.4.0.tgz
cd kafka_2.12-2.4.0
```

#### server.properties 파일 수정
- 파일 내용 중 주석처리되어있는 advertised.listeners 속성 설정 값을 아래와 같이 추가해준다.
  ```bash
  advertised.listeners=PLAINTEXT://localhost:9092
  ```

#### 서버 실행
- 주키퍼 서버 실행: `bin/zookeeper-server-start.sh config/zookeeper.properties`
- 카프카 서버 실행: `bin/kafka-server-start.sh -daemon config/server.properties`

#### test 토픽 생성
- 주키퍼 서버에 TopicCommand 명령을 이용한 토픽 생성 요청 (파티션 하나)
  ```bash
  bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
  ```
- 카프카 서버에 카프카 콘솔 프로듀서 스크립트를 통해 메시지 전송을 위한 ConsoleProducer 실행 요청
  ```bash
  bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
  ```
- \> 커서와 함께 입력 대기중인 상태를 확인한 뒤, 보낼 메시지를 `test command 1`, `test command 2`와 같은 식으로 각각 입력 뒤 엔터

#### test 토픽 메시지 가져오기
- 카프카 콘솔 컨슈머를 통해 다음 명령을 전달하여 test 토픽에 등록된 모든 데이터 가져오기
  ```bash
  bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
  ```

## 파티션을 가지는 토픽 예제
#### 파티션 8개를 사용하도록 지정된 test8 토픽 생성
```bash
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 8 --topic test8
```

#### test8 토픽에 순차적으로 1~8까지의 메시지 각각 입력 후 엔터
```bash
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test8
```

#### test8에 등록된 토픽 메시지 가져오기
```bash
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test8 --from-beginning
```

#### 위 결과를 통해 얻어온 값은 입력한 순서대로 돌아오지 않음
- 파티션과 각 파티션 별 메시지 순서를 이해해야함
- 컨슈머 입장에서 불러올 때에는 파티션이 1개인 토픽에 대해서만 순서가 보장되며, 파티션이 여러개인 경우 저장된 순서대로 메시지를 가져올 수 없다

## 파티션 4개를 가지는 토픽 예제
#### 파티션 4개를 사용하도록 지정된 test4 토픽 생성
```bash
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 4 --topic test4
```

#### test4 토픽에 순차적으로 1~6까지의 메시지 각각 입력 후 엔터
```bash
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test4
```

#### test4에 등록된 토픽 메시지 가져오기
```bash
# 현재 컴퓨터에서는 4,5,6,1,2,3 식으로 응답됨
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test4 --from-beginning
```

#### 파티션이 여러개인 경우 저장 순서대로 메시지가 응답됨을 보장하지 않음
- 파티션이 하나인 경우 메시지 순서가 보장됨
- 파티션이 여러개인 경우, 파티션 별 저장되어있는 메시지의 순서만 보장되므로 전체 저장된 메시지의 순서는 보장받을 수 없음

#### 참고문서
- [https://www.popit.kr/kafka-운영자가-말하는-처음-접하는-kafka](https://www.popit.kr/kafka-%EC%9A%B4%EC%98%81%EC%9E%90%EA%B0%80-%EB%A7%90%ED%95%98%EB%8A%94-%EC%B2%98%EC%9D%8C-%EC%A0%91%ED%95%98%EB%8A%94-kafka/)

<div class="fb-comments" data-href="https://fureweb-com.github.io{{page.url}}" data-width="100%" data-numposts="10"></div>

<div id="fb-root"></div>
<script>(function(d, s, id) {
  var js, fjs = d.getElementsByTagName(s)[0];
  if (d.getElementById(id)) return;
  js = d.createElement(s); js.id = id;
  js.src = "//connect.facebook.net/ko_KR/sdk.js#xfbml=1&version=v2.10&appId=403216550080274";
  fjs.parentNode.insertBefore(js, fjs);
}(document, 'script', 'facebook-jssdk'));</script>
