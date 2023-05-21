---
layout: post
title: 'MongoDB의 Change Streams - 개념편'
subtitle: 'java'
date: 2023-05-21 19:58:00
categories: devlog
tags: mongodb 글또 db
---

최근 MongoDB를 쓸 일이 생기면서 여러가지 기능들을 사용하게 되었는데 그 중 하나인 `Change Streams` 에 대해 알아보고자 한다.

해당 글은 *개념편과 실전편으로 나누어 작성할 것*이다!

# 이 기능은 뭐지?

![mongodb-change-streams](https://swagger.synology.me:5543/uploads/2023/05/mongodb-change-streams.png)

이 기능은 MongoDB에서 **실시간으로 데이터 변경을 추적하여 해당 변경 사항에 대응하는 이벤트를 추적할 수 있는 기능**이며 MongoDB v3.6에서 추가되었다!

간단히 말하면 **CDC(Change Data Capture)와 비슷한데 MongoDB가 Publisher, 전달받을 앱이 Subscriber가 되는 방식**이다.

![MongoDB 변경 스트림 - MongoDB Oplogs.](https://swagger.synology.me:5543/uploads/2023/05/32506503-0923a1cc-c3e5-11e7-8e84-079b6e3559d5.png)

즉, 데이터가 삽입되거나 변경, 삭제 같은 데이터 변경을 구독하고 반응할 수 있다. 또한 이런 이벤트는 필터링 처리가 가능하기 때문에 특정 변경사항만 받아보거나 할 수도 있다.

<br/>

## CDC란?

CDC는 **C**hange **D**ata **C**apture 의 앞글자만 딴 줄임말인데, **데이터베이스 내에서 데이터에 대한 변경을 식별해 필요한 후속처리를 자동화 하는 기술**이다.

간단히 말하면 DB에서 어떠한 데이터가 변경이 되었을 때, 그 데이터를 활용하여 추가적으로 어떠한 동작이나 기능을 취할 수 있도록 한다.

해당 CDC를 활용한 방법 중 우리에게 친숙한 기능들은 Trigger, 로그 스캐너 등이 있는데.. 더욱 자세한 설명은 [이곳](https://ko.wikipedia.org/wiki/%EB%B3%80%EA%B2%BD_%EB%8D%B0%EC%9D%B4%ED%84%B0_%EC%BA%A1%EC%B2%98) 을 참조!

<br/>

## 디테일한 기능

### 1. 필터링 가능

나에게 필요한 변경 건만 수신할 수 있도록 변경 사항을 필터링 할 수 있다.

내가 데이터의 삭제 이벤트만 수신하고 싶다면 삭제 이벤트만 받아 볼 수 있는 것이다!

### 2. 재개 가능

각 응답은 Resume Token과 함께 제공되기 때문에 해당 토큰을 활용하여 연결이 끊어진 경우 중단된 시점부터 다시 진행이 가능하다.

### 3. 순차적

데이터의 업데이트 된 순서대로 변경 이벤트가 수신된다.

### 4. 지속성

해당 기능은 커밋된 변경만 포함된다.

### 5. 보안

컬렉션을 읽을 수 있는 권한이 있는 사용자만 해당 컬렉션에서 해당 기능을 사용할 수 있다.

### 6. 쉬운 사용

해당 기능의 API 문법은 기존 MongoDB 와 동일한 드라이버와 동일한 쿼리 언어를 사용한다.

<br/>

## 사용 가능한 단위

| 단위         | 설명                                                                                                            |
| ------------ | --------------------------------------------------------------------------------------------------------------- |
| 컬렉션       | 단일 컬렉션 단위로 변경을 감시할 수 있다. (단 system, admin, local, config 데이터베이스 제외)                   |
| 데이터베이스 | v4.0 이상부터 데이터베이스 단위로 예외 데이터베이스를 제외한 모든 컬렉션의 변경사항을 감시할 수 있다.           |
| Deployment   | v4.0 이상부터 Deployment 단위로 커서를 열어 예외 데이터베이스를 제외한 모든 컬렉션의 변경사항을 감시할 수 있다. |

---

<br/>

# 사용하기 위한 요구사항

1. 해당 기능은 Replica Set 혹은 Shard Cluster에서만 사용가능하다.
2. WiredTiger 스토리지 엔진을 사용해야 한다. (v3.2부터 기본 엔진으로 제공되기 때문에 추가 세팅이 없다면 문제 없다.)
3. Replica Set 프로토콜 버전1(pv1) 을 사용해야 한다.
4. v4.0 이하일 경우 'majority' 옵션을 활성화 해야 한다.

<br/>

## 사용가능한 환경

- Python
- Java (Sync)
- Node.js
- PHP
- Motor
- C
- C#
- Ruby
- Go

---

<br/>

이번편에서는 MongoDB의 Change Streams에 대한 개념적인 부분을 알아보았다. 다음 _'실전편'_ 에서는 MongoDB 레플리카셋을 도커로 띄워 Java와 C#으로 이벤트를 구독하는 방법을 알아보겠다!

---

<br/>

# 참고

[변경 데이터 캡처](https://ko.wikipedia.org/wiki/%EB%B3%80%EA%B2%BD_%EB%8D%B0%EC%9D%B4%ED%84%B0_%EC%BA%A1%EC%B2%98)

[Change Streams공식 문서](https://www.mongodb.com/basics/change-streams)

[2 Easy Steps To Master MongoDB Change Streams](https://hevodata.com/learn/mongodb-change-streams-real-time-data/)

[3 Ways to Offload Read-Heavy Applications from MongoDB](https://rockset.com/blog/3-ways-to-offload-read-heavy-applications-from-mongodb/)
