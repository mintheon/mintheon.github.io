---
layout: post
title: [Effective Java 뽀개기] 01. 생성자 대신 정적 팩터리 메서드를 고려하라
subtitle: 'java'
date: 2022-09-04 21:22:50
categories: devlog
tags: java study
---

![image-20220904212556629](https://tva1.sinaimg.cn/large/e6c9d24egy1h5uufg0s7hj21do0ks0w4.jpg)

> 해당 파트의 정적 팩터리 메서드는 디자인 패턴에서의 팩터리 메서드와 다르다. 디자인 패턴중엔 이와 일치하는 패턴은 없다.

클래스는 클라이언트에 public 생성자 (or 생성자) 대신 정적팩터리 메서드를 제공할 수 있다.

<br/>

# 장점

<br/>

## 1. 이름을 가질 수 있다.

생성자에 넘기는 매개변수와 생성자로는 반환 객체의 특성을 제대로 설명할 수 없지만, 정적팩터리는 이름만 잘 짓는다면 반환 객체의 특성을 쉽게 묘사할 수 있다.

**_예시 (값이 소수인 BigInteger를 반환하는 코드)_**

```java
//Bad: 생성자 사용
new BigInteger(int, int, Random);

//Good: 정적 팩터리 메서드 사용 (더 특성이 명확하다)
BigInteger.probablePrime(int, int, Random);
```

또한 하나의 시그니처로는 생성자를 하나만 만들 수 있다. 살짝 틀어 여러개의 생성자를 만들 수는 있지만 정확히 어떤 역할을 하는것인지 기억하기 어려워 엉뚱한 것을 호출하는 실수를 할 수 있다.

**한 클래스에 시그니처가 같은 생성자가 여러개 필요할 것 같다면, 생성자를 정적 팩터리 메서드로 바꾸고 각 차이를 잘 드러내는 이름을 지어주자.**

<br/>

## 2. 호출될 때마다 인스턴스를 새로 생성하지 않아도 된다.

이부분으로 **불변 클래스는 인스턴스를 미리 만들어 두거나 캐싱하여 재활용하는 식으로 불필요한 객체 생성을 피할 수 있다.**

_(ex)_ `Boolean.valueOf(boolean)` 메서드는 객체를 아예 생성하지 않는다. 따라서 **생성 비용이 있는 객체가 자주 요청되는 상황이면 성능에 도움**이 된다.

(비슷한 패턴) Flyweight pattern

또한 **같은 객체를 반환하는 방식으로 인스턴스 통제 클래스 클래스로 사용할 수 있다.**

인스턴스를 통제하면 singleton 혹은 인스턴스화 불가(noninstantiable)로 만들 수 있다. 또한 불변 값 클래스에서 동치인 인스턴스가 단 하나뿐임을 보장할 수 있다. (equals)

이러한 인스턴스 통제는 플라이웨이트 패턴, 열거 타입의 근간이 된다.

<br/>

## 3. 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.

API를 만들 때 해당 유연성을 응용하면 **구현 클래스를 공개하지 않고도 그 객체를 반환할 수 있어 API를 작게 유지할 수 있다.** 이는 인터페이스 기반 프레임워크를 만드는 핵심 기술이다.

<br/>

**자바 8 이전**

인터페이스에 정적 메서드를 선언할 수 없어 동반 클래스를 만들어 내부에 정의하였다. 덕분에

_(ex)_ 자바 컬렉션 프레임 워크(`java.util.Collections`) 내의 정적 팩터리 메서드

<br/>

**자바 8 이후**

인터페이스 내 정적 메서드 제한이 풀려 정적 멤버들을 인터페이스 자체에 둔다.

대신 **대부분의 정적메서드 구현 코드는 별도 package-private 클래스에 두어야 할 수밖에 없다.**

자바 8에서는 public 정적 멤버만 허용한다. 자바 9에서는 private 정적 메서드까지 허락하지만 정적 필드와 정적 멤버 클래스는 public 이어야한다.

<br/>

## 4. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.

반환타입의 하위타입이기만 하면 어떤 클래스의 객체를 반환하든 상관 없다.

클라이언트는 팩터리가 건네주는 객체가 어느 클래스의 인스턴지인지 알 수도 없고 알 필요도 없다. 단순 하위 클래스기만 하면 된다.

_(ex)_ EnumSet 클래스

<br/>

## 5. 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.

이런 유연함은 **서비스 제공자 프레임워크(service provider framework)를 만드는 근간**이 된다.

→ 제공자(provider)는 서비스의 구현체인데, 이런 구현체들을 클라이언트에 제공하는 역할을 프레임워크가 통제하여 클라이언트를 구현체로부터 분리해준다.

서비스 제공자 프레임워크는 3개 + 1개의 핵심 컴포넌트로 이뤄진다.

1. 서비스 인터페이스
   - 구현체의 동작을 정의
2. 제공자 등록 API
   - 제공자가 구현체를 등록 할 때 사용
3. **서비스 접근 API → ‘상기의 유연한 정적 팩터리’**
   - 클라이언트가 서비스의 인스턴스를 얻을 때 사용
4. (종종사용) 서비스 제공자 인터페이스
   - 서비스 인터페이스의 인스턴스르 생성하는 팩터리 객체를 설명
   - 없다면 각 구현체를 인스턴스로 만들 때 리플렉션을 사용해야 한다.

_(ex)_

1. JDBC
   - Connection → 서비스 인터페이스 역할
   - DriverManager.registerDriver → 제공자 등록 API 역할
   - DriverManager.getConnection → 서비스 접근 API 역할
   - Driver → 서비스 제공자 인터페이스 역할
2. 브릿지 패턴 (Bridge pattern)
3. 의존 객체 주입(DI) 프레임워크

<br/>

# 단점

<br/>

## 1. 정적 팩터리 메서드만 제공시 하위 클래스를 만들 수 없다.

상속을 하려면 `public` 이나 `protected` 생성자가 필요하기 때문이다.

다른 관점에서 생각하면 해당 제약은 **상속보다 컴포지션을 사용하도록 유도하고, 불변타입으로 만들려면 해당 제약을 지켜야 한다는 점에서 오히려 장점이 될 수도 있다.**

<br/>

## 2. 정적 팩터리 메서드는 프로그래머가 찾기 어렵다.

생성자처럼 API설명에 명확히 드러나지 않기 때문이다.

**API 문서를 잘 쓰고, 또한 메서드 이름도 알려진 규약을 따라 짓는식으로 문제를 완화시켜야 한다.**

<br/>

### 정적 패터리 메서드 네이밍 컨벤션

- **from**
  - 매개변수를 하나 받아 해당 타입의 인스턴스를 반환하는 형변환 메서드
  - `Date d = Date.from(instant);`
- **of**
  - 여러 매개변수를 받아 적합 타입 인스턴스를 반환하는 집계 메서드
  - `Set<Rank> faceCards = EnumSet.of(JACK, QUEEN, KING);`
- **valueOf**
  - from과 of의 더 자세한 버전
  - `BigInteger prime = BigInteger.valueOf(Integer.MAX_VALUE);`
- **instance 혹은 getInstance**
  - 매개변수로 명시한 인스턴스를 반환하지만 같은 인스턴스임을 보장 X
  - `StackWalker luke = StackWalker.getInstance(options);`
- **create 혹은 newInstance**
  - instance, getInstance와 같지만 매번 새로운 인스턴스를 생성해 반환함을 보장
  - `Object newArray = Array.newInstance(classObject, arrayLen);`
- **getType**
  - getInstance와 같지만 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할때 사용.
  - ‘Type’ 부분은 팩터리 메서드가 반환할 객체의 타입이다.
  - `FileStore fs = Files.getFileStore(path)`
- **newType**
  - getType과 동일하게 사용하며, newInstance와 같다.
  - `BufferedReader br = Files.newBufferedReader(path)`
- **type**
  - getType과 newType의 간결한 버전
  - `List<Complaint> litany = Collections.list(legacyLitany)`

<br/>

# 요약

정적 팩터리 메서드와 public 생성자는 각 쓰임새에 따라 장단점을 이해하고 사용하자.

하지만 대부분 정적 팩터리를 사용하는게 유리한 경우가 더 많다.
