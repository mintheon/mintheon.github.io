---
layout: post
title: 'String과 new String의 차이, 그리고 StringPool이란?'
subtitle: 'java'
date: 2021-12-05 19:26:00
categories: devlog
tags: java 글또
---

![image-20210815114036054](https://tva1.sinaimg.cn/large/008i3skNgy1gxiibhkaahj31e80l4wfz.jpg)

예전에 CS 개념 모음집을 보다가 **'`String a = "hello";` 와 `String a = new String("hello");` 의 차이점은 무엇일까요?'** 라는 얘기에 _객체를 새로 만드냐 안만드냐의 차이_ 정도로 숙지하고 넘어갔던 기억이 있다.

<br/>

솔직히 해당 질문을 보기 전까진 딱히 생각해본적이 없던 문제였다.

![img](https://tva1.sinaimg.cn/large/008i3skNgy1gxiibi06paj30dw0fjwfr.jpg)

요새 cs에 부족함을 많이 느껴 기본기를 다시금 다져보자는 차원에서 기본서들을 보고있는데 해당 부분이 나와 정리해보고자 한다.

<br/>

---

# String은 불변이다.

> 불변객체란, 객체가 생성된 후 내부 상태가 변하지 않고 계속 유지되는 객체를 말한다. 즉, 변수에 객체가 한 번 할당되면 해당 객체의 참조를 변경할 수도, 내부 상태를 수정할 수도 없다.

```java
String str = "문자열";
str = "문자열2";
```

![img](https://tva1.sinaimg.cn/large/008i3skNgy1gxiibf1593j30oj0ds0tq.jpg)

위 코드는 그림과 같이 처리된다.

`str` 이라는 객체에 "문자열"이라는 값을 넣고, "문자열2"로 바꾸게 된다면 **기존 주소 100번의 값이 변경된 것이 아닌, 새로운 String 객체가 생성되어 그 참조(주소값 200)가 `str` 변수에 할당** 된다.

그래서 즉 이 상태에서는 최초에 생성된 "문자열", "문자열2" 두개의 객체가 heap에 생성되어 있는 상태이다. ("문자열"은 참조가 끊어져 GC의 대상이 된다.)

<br/>

## String이 불변객체인 이유

String 타입은 가장 많이 사용되는 데이터 타입중 하나이다. 그 말은 **String 타입의 객체들이 가장 많은 메모리를 차지한다**는 말과 같다.

그래서 **문자열 객체는 재사용 될 가능성이 높기에 같은 값일 경우 어플리케이션당 하나의 String 객체만을 생성하여 JVM의 heap 공간을 절약**하고자 했다.

<br/>

## 그래서 만들어낸 String Pool

**Java의 Heap 내에는 `String Pool` 이라는 특별한 영역에서 String 객체들을 관리**한다. 이 `String Pool` 은 사실 `HashMap` 과 같다.

```java
String s1 = "Hello";
String s2 = "Hello";
String s3 = "Bye";
String s4 = new String("Hello");
```

리터럴을 이용한 String 생성과 new 연산자를 이용한 String 생성의 차이는 무엇일까?

**가장 큰 차이점은 객체가 생성되는 영역**이다. 위 코드는 아래와 같이 처리된다.

![image-20211205191049073](https://tva1.sinaimg.cn/large/008i3skNgy1gxiibfs4fgj31i50qkgom.jpg)

- String literal로 생성한 객체는 `String Pool` 에 들어간다.
- String lieteral로 생성한 객체의 값이 이미 `String Pool` 에 존재한다면 해당 객체는 `String Pool`의 레퍼런스를 참조한다.
- `new` 연산자로 생성한 String 객체는 같은 값이 `String Pool` 에 이미 존재하더라도 `Heap영역` 내의 별도 객체를 가리킨다.

<br/>

### String Pool의 위치는?

위의 그림에선 Heap 내에 존재하는 것으로 되어있으나 이부분은 **`Java7` 이후 버전 기준**이다.

![img](https://tva1.sinaimg.cn/large/008i3skNgy1gxiibgc1cnj30pk06aq3a.jpg)

이전 버전의 경우 `JVM Heap 내부의 PermGen` 영역에 있었는데, 해당 영역의 경우 객체가 가득 찬 상태에서 `Runtime(실행중)` 환경에서는 메모리를 동적으로 늘리지 못해 영역이 가득 차면 `OutOfMemory` 에러가 발생했다.

이런 위험성을 줄이기 위해 `Perm` 영역이 아닌 `Heap` 에 `String Pool` 을 생성한다.

<br/>

---

# 결론

![img](https://tva1.sinaimg.cn/large/008i3skNgy1gxiibh5y8gj30gy0dw757.jpg)

String 객체를 **`new 연산자`로 생성하면 같은 값이어도 매번 새로운 객체가 생성**되게 한다. 그리하여 **String이 갖는 불변성이라는 장점을 누리지 못하게된다.**

```
메모리를 효율적으로 사용하기 위한 기능인 만큼, String은 항상 리터럴(큰따옴표) 방식으로 생성하는 것이 좋다!
```

<br/>

---

### 출처

[[Java] String의 불변성(Immutable)과 그 이유](https://dololak.tistory.com/699)

[String Constant Pool이란? Java String Pool](https://starkying.tistory.com/entry/what-is-java-string-pool)

[[Java] Java의 문자열(String) 객체가 저장되는 String Pool에 대하여](https://dololak.tistory.com/718)

[[JAVA]자바의 기본 개념 정리-4.String pool](https://doohong.github.io/2018/03/04/java-string-pool/)

자바의 정석
