---
layout: post
title: [Effective Java 뽀개기] 02. 생성자에 매개변수가 많다면 빌더를 고려하라
subtitle: 'java'
date: 2022-09-04 21:22:50
categories: devlog
tags: java study
---

![image-20220904212556629](https://tva1.sinaimg.cn/large/e6c9d24egy1h5uufg0s7hj21do0ks0w4.jpg)

정적팩터리와 생성자에는 선택적 매개변수가 많을 때 적절히 대응하기 어렵다. 이 경우 아래와 같은 방식으로 해결해 왔다.

<br/>

# 1. 점층적 생성자 패턴

필수 매개변수만 받는 생성자, 필수 + 선택 1개를 받는 생성자 … 형태로 **매개변수의 개수대로 생성자를 만드는 방식**이다.

```java
public class User {
	private final String name;
	private final int age;
	private final String phone;

	public User(int name, int age) {
		this(name, age);
	}

	public User(String name, int age, String phone) {
		this(name, age, phone);
	}
}
```

<br/>

## 단점

1. **매개변수 개수가 많아지면 클라이언트 코드를 작성하거나 읽기가 어려워진다.**
2. 매개변수에 딱 알맞은 생성자가 없다면 불필요한 매개변수에 임의의 값을 지정해주어야 한다.

이러한 부분으로 실수가 잦아질 수 있으며, 버그로 이어질 수 있다.

<br/>

# 2. 자바빈즈 패턴

매개변수가 하나도 없는 생성자로 객체를 만든 후, **Setter 메서드 들을 호출해 원하는 매개변수의 값을 설정하는 방식**이다.

```java
public class User {
	private final String name;
	private final int age;
	private final String phone;

	public User() {}

	public void setName(String name) { this.name = name; }
	public void setAge(String age) { this.age = age; }
	public void setPhone(String phone) { this.phone = phone; }
}
```

<br/>

## 단점

1. 객체 하나를 만들기 위해 메서드를 여러개 호출해야 한다.
   - 객체가 완전히 생성되기 전 까지는 일관성(consistency)가 무너진 상태에 놓인다.
   - 클래스를 불변으로 만들 수 없다.

이러한 단점을 완화하고자 생성이 끝난 객체를 수동으로 ‘얼리고(freezing)’ 얼리기 전에는 사용할 수 없도록 한다.

→ JavaScript를 사용할때 const 사용이 불가할 때 freeze를 사용해봤지만 여간 불편한게 아니였다.

<br/>

# 3. 빌더 패턴 (해결사)

클라이언트가 필요한 객체를 직접 만드는 대신, **필수 매개변수만으로 생성자를 호출해 빌더 객체를 얻는다. 이후에 빌더 객체가 제공하는 일종의 세터 메서드들로 원하는 선택 매개변수들을 설정**한다. 마지막으로 `build` 메서드를 호출해 객체를 얻는다.

```java
public class User {
	private final String name; //필수
	private final int age; //필수

	private final String phone;//선택

	public static class Builder {
    private final String name;
    private final int age;
    private final String phone;

    public Builder(String name, int age) { //필수
      this.name = name;
      this.age = age;
    }

    public Builder phone(String phone) { //선택
      this.phone = phone;
      return this;
    }

    public User build() {
      return new User(this);
    }
  }

  private User(Builder builder) {
    this.name = builder.name;
    this.age = builder.age;
    this.phone = builder.phone;
  }
}
```

<br/>

빌더 패턴을 사용하면 **불변 클래스를 만들 수 있다.** 빌더의 세터 메서드들은 빌더 자신을 반환하기 때문에 **메서드 체이닝도 가능**하다.

> **불변(immutable)이란 어떠한 변경도 허용하지 않는다는 뜻**이다. 대표적으로 String 객체가 있다.
> **불변식(invariant)이란 정해진 기간동안 반드시 만족해야 하는 조건**을 말한다. 예컨대 리스트의 크기는 반드시 0 이상이어야 하니 한순간이라도 음수값이 된다면 불변식이 깨진것이다.

<br/>

해당 빌더 패턴은 파이썬과 스칼라에 있는 명명된 선택적 매개변수(named optional parameters)를 흉내낸 것이다.

아래는 빌더 패턴을 사용한 예시이다.

```java
User minhyun = new User.Builder("minhyun", 99)
	.phone("010-0000-0000")
	.build();
```

<br/>

## 장점

1. 쓰기 쉽고, 읽기 쉽다.
2. 계층적으로 설계된 클래스와 함께 쓰기 좋다. (책에 피자로 든 예제가 있다.)
3. 가변인수(varargs) 매개변수를 여러개 사용할 수 있다.
4. 생성에 유연하다.
   1. 빌더 하나로 여러 객체를 순회하면서 만들 수 있따.
   2. 매개변수에 따라 다른 객체를 만들 수 있다.
   3. 일련번호와 같은 특정 필드는 빌더가 알아서 채우도록 할 수도 있다.

<br/>

## 단점

1. 객체를 만들기 전 빌더부터 만들어야 한다.
   1. 생성비용이 크지는 않지만 성능에 민감한 상황에선 문제가 될 수 있다.
2. 코드가 장황하여 매개변수가 4개 이상은 되어야 값어치를 한다.
   1. 하지만 API는 시간이 지날수록 매개변수가 많아지는 경향이 있으니 큰 문제는 없다.

<br/>

# 정리

**생성자나 정적 팩터리가 처리해야 할 매개변수가 많다면 빌더 패턴을 선택하는게 낫다.** 점층적 생성자보다 코드를 읽고 쓰기가 훨씬 간결하고, 자바빈즈보다 훨씬 안전하다.
