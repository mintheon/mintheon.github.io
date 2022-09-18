---
layout: post
title: '[Effective Java 뽀개기] 07. 다 쓴 객체 참조를 해제하라'
subtitle: 'java'
date: 2022-09-28 23:52:50
categories: devlog
tags: java study
---

![image-20220904212556629](https://tva1.sinaimg.cn/large/e6c9d24egy1h5uufg0s7hj21do0ks0w4.jpg)

C, C++과 다르게 자바는 가비지 컬렉터를 갖추어 자원관리에서 조금 더 편하다.

하지만 메모리관리에 더이상 신경쓰지 않아도 되는것은 아니다. 아래는 메모리 누수의 예시이다.

<br/>

# 메모리 누수의 예시

## 1. 자기 메모리를 직접 관리하는 클래스

- **메모리 누수가 진행되는 스택 코드**

  ```java
  public class Stack {
      private Object[] elements;
      private int size = 0;
      private static final int DEFAULT_INITIAL_CAPACITY = 16;

      public Stack() {
          elements = new Object[DEFAULT_INITIAL_CAPACITY];
      }

      public void push(Object e) {
          ensureCapacity();
          elements[size++] = e;
      }

      public Object pop() {
          if (size == 0)
              throw new EmptyStackException();
          return elements[--size];
      }

      /**
       * 원소를 위한 공간을 적어도 하나 이상 확보한다.
       * 배열 크기를 늘려야 할 때마다 대략 두 배씩 늘린다.
       */
      private void ensureCapacity() {
          if (elements.length == size)
              elements = Arrays.copyOf(elements, 2 * size + 1);
      }
  ```

  - 스택에서 값을 꺼낼 때(pop()) 값만 꺼내고 있지 해당 배열 값은 계속 참조가 되고 있기 때문에 가비지 컬렉터가 회수하지 않는다.
    → elements 배열의 size보다 컸던 원소들

<br/>

- **제대로 구현한 스택 코드**
  ```java
  ...
  public Object pop() {
  	if (size = 0)
  		throw new EmptyStackException();
    Object result = elements[--size];
  	elements[size] = null; // 다 쓴 참조 해제
  	return result;
  }
  ...
  ```

위와 같은 예시로 처리했지만 그렇다고 모든 객체 참조를 null 처리 할 필요는 없다. **객체 참조를 null 처리하는 일은 예외적인 경우여야 한다.**

**다쓴 참조를 해제하는 최고의 방법은 그 참조를 담은 변수를 유효 범위(scope) 밖으로 밀어내는 것**이다.

→ 변수의 범위를 최소가 되게 정의하자. (아이템 57)

위 Stack 클래스가 메모리 누수에 취약한 이유는 **자기 메모리를 직접 관리했기 때문**이다.

`elements` 배열로 저장소 풀을 만들어 원소들을 관리했는데 따로 배열의 활성 영역, 비활성 영역을 가비지 컬렉터는 알 길이 없다.

→ 가비지 컬렉터가 보기엔 참조되어 있는 객체들은 모두 유효한 객체이기 때문에 사용하지 않는 값은 null 처리해주어야 한다.

**자기 메모리를 직접 관리하는 클래스라면 프로그래머는 항시 메모리 누수에 주의해야 한다.**

<br/>

## 2. 캐시

객체 참조를 캐시에 넣고나서, 까먹어 버려 객체를 그대로 계속 놔두는 일을 접할 수 있다. 이경우 해결방법은 여러가지이다.

<br/>

### 1. 키(key)를 참조하는 동안만 캐시가 필요한 경우 WeakHashMap 사용

대신 `WeakHashMap` 은 키를 참조하는 동안만 캐시가 필요한 상황에서만 유리하다.

<br/>

### 2. 시간이 지날수록 가치를 떨어뜨리는 방식

캐시를 만들때 보통 캐시 엔트리의 유효기간을 정확히 정의하기 어렵기 때문에 시간이 지날수록 엔트리의 가치를 떨어뜨린다.

이런 경우 **쓰지않는 엔트리를 이따금 청소해줘야 하기 때문에(리텐션) 백그라운드 캐시를 활용하거나, 새 엔트리를 추가할때 부수작업으로 수행**한다.

→ LinkedHashMap은 `removeEldestEntry` 메서드를 사용하여 후자의 방식으로 처리한다.

<br/>

## 3. 리스너 혹은 콜백

클라이언트가 콜백을 등록만하고 명확히 해지하지 않으면 콜백은 쌓여갈 것이다.

이때, 콜백을 약한 참조(weak reference)로 저장하면 가비지 컬렉터가 즉시 수거해 간다.

→ (ex) WeakHashMap에 키로 저장하는 방법

<br/>

# 정리

메모리 누수는 겉으로 잘 드러나지 않아 시스템에 수년간 잠복하는 사례도 있다. **이런 종류의 문제는 예방법을 익혀두는 것이 매우 중요**하다.
