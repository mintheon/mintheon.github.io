---
layout: post
title: '[Effective Java 뽀개기] 11. equals를 재정의하려거든 hashCode도 재정의하라'
subtitle: 'java'
date: 2022-10-02 23:23:31
categories: devlog
tags: java study
---

![image-20220904212556629](https://tva1.sinaimg.cn/large/e6c9d24egy1h5uufg0s7hj21do0ks0w4.jpg)

기존에 작성한 블로그 글과 거의 일치하여 해당건은 블로그 글로 대체함. 추가적으로 적고싶은 부분만 더 적겠음.

[(링크)](<https://mintheon.com/devlog/2021/09/26/equals()%EC%99%80-hashcode()%EB%A5%BC-%EC%96%B8%EC%A0%9C-%EC%A0%95%EC%9D%98%ED%95%A0%EA%B9%8C/>)

# 좋은 hashCode를 작성하는 요령

1. int 변수 result를 선언한 후 값 c로 초기화 한다.
   1. 이때 c는 해당 객체의 equals 비교에 사용되는 피연산자를 단계 2.a 방식으로 계산한 해시코드다.
2. 해당 객체의 나머지 핵심 필드 f에 각각 다음 작업을 수행한다.
   1. 해당 필드의 해시코드 c를 계산한다.
      1. 기본타입일 경우
         - `Type.hashCode(f)` (Type == 해당 기본타입의 방식클래스)
      2. 참조타입일 경우 혹은 이 클래스의 equals 메서드가 필드의 equals를 재귀 호출할 경우
         - 필드의 hashCode를 재귀적으로 호출한다.
         - 계산이 복잡해질것 같다면 표준형을 만들어 해당 표준형의 hashCode를 호출한다.
      3. 배열일 경우
         1. 핵심 원소 각각을 별도 필드처럼 다룬다. 모든 원소가 핵심 원소라면 `Arrays.hashCode` 를 사용한다.
   2. 단계 2.a에서 계산한 해시코드 c로 result를 갱신한다.

      ```java
      result = 31 * result + c;
      // 31을 곱하는 이유: 필드를 곱하는 순서에 따라 result값이 달라진다.
      //                곱셈이 없다면 아나그램의 해시코드가 같아진다.
      //                굳이 31인 이유는 홀수이면서 소수이기 때문이다. (2를 곱하는건 쉬프트 연산과 같기 때문)
      ```
3. result를 반환한다.

`**Objects.hash()` 를 사용하여 위의 해쉬코드를 단 한줄로 처리할 수 있으나 속도가 조금 느리다.\*\*
