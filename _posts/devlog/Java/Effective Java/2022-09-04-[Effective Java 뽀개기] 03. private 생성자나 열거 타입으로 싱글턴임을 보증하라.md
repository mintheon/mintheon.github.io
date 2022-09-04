---
layout: post
title: [Effective Java 뽀개기] 03. private 생성자나 열거 타입으로 싱글턴임을 보증하라
subtitle: 'java'
date: 2022-09-04 21:22:50
categories: devlog
tags: java study
---

![image-20220904212556629](https://tva1.sinaimg.cn/large/e6c9d24egy1h5uufg0s7hj21do0ks0w4.jpg)

> 싱글턴이란 인스턴스를 오직 하나만 생성할 수 있는 클래스를 말한다. 전형적인 예로는 함수와 같은 무상태 객체나 유일해야하는 시스템 컴포넌트를 들 수 있다.

클래스를 싱글턴으로 만들면 이를 사용하는 클라이언트를 테스트하기가 어려워 질 수 있다.

<br/>

# 싱글턴을 만드는 방식

두 방식 모두 생성자는 private로 감춰두고, 유일한 인스턴스에 접근할 수 있는 수단으로 public static 멤버를 하나 둔다.

<br/>

## 1. public static final 필드 방식의 싱글턴

```java
public class Tom {
	public static final Tom INSTANCE = new Tom();
	private Tom() { ... }

	public void goToSchool() { ... }
}
```

- private 생성자는 Tom.INSTANCE를 초기화 할 때 딱 한번만 호출되기 때문에 protect나 public 생성자가 없으므로 **초기화될 때 만들어진 인스턴스가 전체 시스템에서 하나뿐임이 보장된다.**
  - 리플렉션을 이용해 private 생성자를 호출할 수 있지만, 두번째 객체가 생성되려 할 때 예외를 던지도록 방어하면된다.

<br/>

### 장점

1. 해당 클래스가 싱글턴임이 API에 명백히 드러난다.
   1. public static 필드가 `final`이라 절대 다른 객체를 참조할 수 없다.
2. 간결하다.

<br/>

## 2. 정적 팩터리 방식의 싱글턴

```java
public class Tom {
	private static final Tom INSTANCE = new Tom();
	private Tom() { ... }
	public static Tom getInstance() { return INSTANCE; }

	public void goToSchool() { ... }
}
```

- `getInstance` 는 항상 같은 객체의 참조를 반환하므로 인스턴스가 하나뿐임이 보장된다.

<br/>

### 장점

1. API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있다.
   1. 유일한 인스턴스를 반환하는 팩터리 메서드가 호출하는 스레드별로 다른 인스턴스를 넘기게 할 수 있다.
2. 정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있다.
3. 정적 팩터리의 메서드 참조를 공급자(supplier)로 사용할 수 있다.
   1. (ex) `Tom::getInstance` → `Supplier<Tom>` 으로 사용 가능하다.

위 장점들이 굳이 필요하지 않다면 public 필드 방식이 좋다.

<br/>

## (1번, 2번방식 해당) 싱글턴 클래스 직렬화

단순히 `Serializable` 을 구현하는것이 아니라 `readResolve` 메서드를 제공해야 한다.

→ 이렇게 하지 않으면 직렬화된 인스턴스를 역직렬화 할 때 마다 새로운 인스턴스가 만들어진다.

```java
// 싱글턴임을 보장해주는 readResolve 메서드
private Object readResolve() {
	// '진짜' Tom을 반환하고, 가짜 Tom은 가비지 컬렉터에 맡긴다.
	return INSTANCE;
}
```

<br/>

## 3. 원소가 하나인 열거 타입 선언

```java
public enum Tom {
	INSTANCE;

	public void goToSchool() { ... }
}
```

<br/>

### 장점

1. 다른 방식보다 간결하고, 추가 노력 없이 직렬화 할 수 있으며, 아주 복잡한 직렬화 상황이나 리플렉션 공격에서도 제 2의 인스턴스가 생기는 일을 완벽히 막아준다.
2. 조금 부자연스럽지만, 대부분 상황에서는 원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법이다.

<br/>

### 단점

1. 만들려는 싱글턴이 Enum 외의 클래스를 상속해야 한다면 이 방법은 사용할 수 없다.
