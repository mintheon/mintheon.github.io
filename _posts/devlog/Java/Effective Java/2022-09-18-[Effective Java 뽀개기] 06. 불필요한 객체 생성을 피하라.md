---
layout: post
title: '[Effective Java 뽀개기] 06. 불필요한 객체 생성을 피하라'
subtitle: 'java'
date: 2022-09-28 23:52:50
categories: devlog
tags: java study
---

![image-20220904212556629](https://tva1.sinaimg.cn/large/e6c9d24egy1h5uufg0s7hj21do0ks0w4.jpg)

객체 하나를 재사용 하는 편이 더 좋다. (특히 불변 객체는 언제든 재사용이 가능하다.)

<br/>

# 재사용의 예시

## 1. String 객체 생성

`new String` 대신 String pool을 이용하도록 코드를 작성하라.

→ 이 내용은 [본인 블로그](https://mintheon.com/devlog/2021/12/05/String%EA%B3%BC-new-String%EC%9D%98-%EC%B0%A8%EC%9D%B4,-%EA%B7%B8%EB%A6%AC%EA%B3%A0-StringPool%EC%9D%B4%EB%9E%80/)에 자세히 설명해두었으니 참고바람.

<br/>

## 2. 정적 팩터리 메서드 사용

boolean 생성자 대신 boolean 정적 팩터리 메서드를 사용하라.

`Boolean(String)` → `Boolean.valueOf(String)`

(boolean 생성자는 자바 9에서 deprecated 되었다.)

<br/>

## 3. 캐싱 사용

생성 비용이 아주 비싼 객체들이 더러 있는데 이런 객체가 반복하여 필요하다면 캐싱하여 재사용하라.

<br/>

### 예시

- **최적화 되지 않은 코드**

  ```java
  static boolean isRomanNumeral(String s) {
  	return s.matches("^...정규식...$");
  }
  ```

  - `String.matches` 는 문자열 형태를 확인하는 가장 쉬운 방법이지만, 성능이 중요할때 반복해서 사용하긴 적합하지 않다.
  - 정규표현식용 Pattern 인스턴스는 한번 쓰고 버려져 바로 가비지 컬렉션 대상이 된다.
  - Pattern은 인스턴스 생성 비용이 높다.

- **최적화 된 코드**
  ```java
  public class RomanNumerals {
  	private static final Pattern ROMAN = Pattern.compile("^...정규식...$");

  	static boolean isRomanNumeral(String s) {
  		return ROMAN.matcher(s).matches();
  	}
  }
  ```
  - 성능이 향상되었다.
  - 패턴 로직을 필드로 꺼내어 이름을 지었기 때문에 코드의 의미가 훨씬 잘 드러난다.

하지만 재사용을 함으로써 덜 명확하거나 안전하지 않은 상황도 있다.

<br/>

# 객체 생성이 불필요한 예시

## 1. 어댑터

어댑터는 실제 작업은 뒷단 객체에 위임하고 자신은 제 2의 인터페이스 역할을 해주는 객체다.

결과적으로 뒷단 객체 외에는 관리할 상태가 없으므로 뒷단 객체 하나당 어댑터 하나씩만 만들어지면 충분하다.

<br/>

### 예시

**Map인터페이스의 KeySet 메서드**

- KeySet이 뷰 객체를 여러개 만들어도 상관은 없지만, 그럴 필요도 없고 이득도 없다.

<br/>

## 2. 오토박싱

기본 타입과 그의 대응하는 박싱된 기본타입의 구분을 흐려주지만 성능에선 별로 좋지 않다.

<br/>

### 예시

Long과 long

- Long을 long으로 바꿔주기만 해도 몇배의 성능 개선을 할 수 있다.
  - Long의 경우 래퍼객체라 Long으로 선언할 경우 인스턴스가 매번 만들어지게 된다.

**박싱된 기본타입보다는 기본타입을 사용하고, 의도치 않은 오토박싱이 숨어들지 않도록 주의하자.**

<br/>

# 정리

**무조건 ‘객체 생성은 비싸니 피해야한다’라는 뜻은 아니다. 아주 무거운 객체가 아니고서야 단순히 객체 생성을 피하고자 객체 풀(pool)을 만들지는 말자.**

최근의 JVM의 가비지 컬렉터는 상당히 잘 최적화 되어서 가벼운 객체를 다룰 땐 직접 만든 객체풀보다 훨씬 빠르다. 그러니 프로그램의 명확성, 간결성, 기능을 위해 객체를 추가로 생성하는 것이면 일반적으로 좋다.

→ DB 연결같은 경우 생성비용이 비싸 재사용하는게 낫지만, 자체 객체 풀은 코드를 헷갈리게 만들고 메모리 사용량을 늘리며 성능을 떨어뜨린다.

**기존 객체를 재사용해야 한다면 새로운 객체를 만들지 마라.**

이번 장은 아이템 50번(새로운 객체를 만들어야 한다면 기존 객체를 재사용하지 마라)과 대조적이지만 정확한 의미를 알자.

방어적 복사가 필요한 상황에서 객체를 재사용했을때의 피해 > 필요없는 객체를 반복 생성했을때의 피해
