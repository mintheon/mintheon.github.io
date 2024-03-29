---
layout: post
title: '[Effective Java 뽀개기] 12. toString을 항상 재정의하라'
subtitle: 'java'
date: 2022-10-02 23:23:32
categories: devlog
tags: java study
---

![image-20220904212556629](https://tva1.sinaimg.cn/large/e6c9d24egy1h5uufg0s7hj21do0ks0w4.jpg)

사실 Object의 기본 `toString` 메서드가 우리에게 유의미한 값을 반환하는 경우는 거의 없다.

단순히 ‘클래스이름@16진수로 표시한 해시코드' 를 반환할 뿐이기 때문이다.

(ex) PhoneNumber@adbbd

**toString 재정의가 중요한 부분은 아니지만 toString을 잘 구현한 클래스는 사용하기에 훨씬 좋고, 디버깅하기도 쉽다.**

<br/>

# 가이드

1. toString은 그 객체가 가진 주요정보 모두를 반환하는게 좋다. 대신 값이 많다면 요약 정보를 담아야 한다.
2. toString이 반환한 값에 포함된 정보를 얻어올 수 있는 API를 제공하라.(getter)

<br/>

# 정리

모든 구체클래스에서 Object의 toString을 재정의하자. 상위 클래스에서 이미 알맞게 재정의한 경우는 예외다.
