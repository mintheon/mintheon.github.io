---
layout: post
title: '[Effective Java 뽀개기] 05. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라'
subtitle: 'java'
date: 2022-09-28 23:52:49
categories: devlog
tags: java study
---

![image-20220904212556629](https://tva1.sinaimg.cn/large/e6c9d24egy1h5uufg0s7hj21do0ks0w4.jpg)

많은 클래스는 하나 이상의 자원에 의존한다. \*\*

_(ex) 맞춤법 검사기 ↔ 사전_

**이런 클래스를 정적 유틸리티 클래스, 혹은 싱글턴으로 구현하는 경우가 흔한데 해당 방식은 그리 완벽하지는 않다.**

→ 맞춤법 검사기를 예로 들면 사전이 언어별로 따로 있을 수 있으며, 테스트용 사전이 필요할 수 있는데 사전 하나로 모든 경우에 대응할 수 없다.

사용하는 자원에 따라 동작이 달라지는 클래스에는 정적 유틸리티 클래스나 싱글턴 방식이 적합하지 않다.

클래스가 여러 자원 인스턴스를 지원하고, 클라이언트가 원하는 자원을 사용할 수 있도록 하는 방식은 **‘인스턴스를 생성할 때 생성자에 필요한 자원을 넘겨주는 방식' 이다. 이는 객체주입의 한 형태**이다.

→ 맞춤법 검사기를 생성할 때 의존 객체인 사전을 주입해주는 방식으로 사용한다.

<br/>

# 의존성 주입의 예시

```java
public class SpellChecker {
	private final Lexicon dictionary;

	public SpellChecker(Lexicon dictionary) {
		this.dictionary = Objects.requireNonNull(dictionary);
	}

	public boolean isValid(String word) { ... }
	public List<String> suggestions(String typo) { ... }
}
```

- 자원이 몇개든 의존 관계가 어떻든 상관없이 잘 작동한다.
- 불변을 보장한다.

<br/>

# 의존성 주입의 응용

## 생성자에 자원 팩터리를 넘겨주는 방식

**자바 8의 `Supplier<T>` 인터페이스가 팩터리를 표현한 완벽한 예**다. 해당 인터페이스를 입력으로 받는 메서드는 일반적으로 한정적 와일드카드 타입(아이템 31) 을 사용해 팩터리 타입 매개변수를 제한해야 한다.

<br/>

## 예시

클라이언트가 제공한 팩터리가 생성한 타일(Tile) 들로 구성된 모자이크를 만드는 메서드

```java
Mosaic create(Supplier<? extends Tile> tileFactory) { ... }
```

의존 객체 주입이 유연성과 테스트 용이성을 개선해주긴 하지만, 의존성이 수천개나 되는 큰 프로젝트에서는 코드를 어지럽게 만든다.

→ 하지만 스프링같은 프레임워크를 사용하면 해결된다. 이러한 프레임 워크들은 의존 객체를 직접 주입하도록 설계된 API를 알맞게 응용해 사용하고 있기 때문이다.

<br/>

# 정리

**클래스가 내부적으로 하나 이상의 자원에 의존하고 해당 자원이 클래스 동작에 영향을 준다면 싱글턴과 정적 유틸리티 클래스 대신 의존 객체 주입 기법을 사용**하자.

의존 객체 주입 기법은 클래스의 유연성, 재사용성, 테스트 용이성을 개선해준다.
