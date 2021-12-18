---
layout: post
title: 'equals()와 hashCode()를 언제 정의할까'
subtitle: 'java'
date: 2021-09-26 19:20:00
categories: devlog
tags: java 글또
---

![image-20210815114036054](https://tva1.sinaimg.cn/large/008i3skNgy1gth9xn4muuj61e80l40u802.jpg)

몇년 전 첫 코딩테스트에서 `equals()`, `hashCode()`를 사용하여 특정 결과가 나오도록 코드를 작성하는 문제가 나왔다.

하필 자바도 제대로 잘 하지 못했고, 막 C언어에서 다른 언어를 배워나가는 참이라 `hashCode()` 메서드 안에 어떤걸 넣어야하는지도 모르고 `equals()`만 만들어 제출했던 기억이 난다.

우연찮게 최근 비슷한 코딩테스트 문제를 풀게 되어 정리하고자 한다.

---

자바를 사용하면서 한번쯤 똑같은 값을 가지고 있는 클래스가 서로 같지 않다고 로직 에러가 난 적이 있었을 것이다.

**엥? 왜지? 클래스 내부의 값은 똑같은데?**

> 자바 내에서는 각각의 객체는 각각의 주소값을 가지고 있고, 각 객체는 주소값으로 판단된다.

<br/>

# 예제

**이름**과 **가격**을 가지고 있는 **물품** 클래스가 있다.

```java
public class Item {
    private String name;
    private int price;

    Item(String name, int price) {
        this.name = name;
        this.price = price;
    }
}
```

<br/>

여기서 `name`과 `price` 값이 동일한 객체를 비교하는 테스트 코드를 작성했다.

```java
@Test
void 두_객체가_같은지_확인() {
   //given
   Item pencil1 = new Item("연필", 500);
   Item pencil2 = new Item("연필", 500);

   //then
   assertThat(pencil1).isEqualTo(pencil2);
}
```

![image-20210926215906371](https://tva1.sinaimg.cn/large/008i3skNgy1guubu8qul9j60hw098wf802.jpg)

![개비스콘](https://tva1.sinaimg.cn/large/008i3skNgy1guuc3f6ijhj60dc0a1mxo02.jpg)

`띠용? 테스트가 실패했다.`

당연하다. 위에서 말했던 대로 각 객체는 주소값을 가지고 있는데, **`equals()` 메서드는 주소값으로 객체의 같음을 판단한다.**

위 결과물을 자세히 보면 **Item@3ec300f1**을 예상했지만 **Item@105fece7**이어서 서로 다르기 때문에 테스트에 실패했다고 뜬다.

<br/>

두 객체를 같다고 판단하게 하려면 `equals()` 메서드를 아래와 같이 **재정의** 해야 한다.

```java
public class Item {
   private String name;
   private int price;

   Item(String name, int price) {
       this.name = name;
       this.price = price;
   }

   @Override
   public boolean equals(Object target) {
      if (this == target) {
         return true;
      }

      if (target == null || !(target instanceof Item)) {
         return false;
      }

      Item item = (Item) target;
      return name.equals(item.name) && price == item.price;
   }
}
```

![image-20210926220319095](https://tva1.sinaimg.cn/large/008i3skNgy1guubyjmsq8j60lc08wweu02.jpg)

![개비스콘](https://tva1.sinaimg.cn/large/008i3skNgy1guubzhsi5qj60du0afwfb02.jpg)

**`name.equals(item.name) && price == item.price` 를 통해 객체는 서로의 주소값을 비교하는 것이 아니고 item과 price를 비교하게 된다.**

---

# equals()

Object 클래스에 정의된 `equals()`는 아래와 같이 정의되어 있다.

```java
public boolean equals(Object obj) {
        return (this == obj);
}
```

즉, 오직 자기 자신과만 같다고 인식하게 되어있다.

<br/>

## 언제 equals를 재정의 해야 하나?

- _논리적으로 동일함을 확인해야 하는데 기존 `equals()`가 재정의 되어있지 않을 때_

  **두 객체를 `equals()`로 비교한다는 것은 객체가 똑같은지가 아니라 값이 같은지 알고싶은 것이기 때문이다.**

<br/>

### equals를 재정의하지 않아도 되는 경우가 있나?

- _인스턴스가 둘 이상 만들어지지 않음을 보장하는 클래스_

  예를 들면 `Enum`과 같은 클래스이다.

  해당 클래스에서는 논리적으로 같은 클래스가 2개이상 만들어 지지 않기 때문에, 논리적 동일함과 객체 식별이 사실상 똑같은 의미가 된다.

<br/>

### equals 메서드 규약

> 해당 규약을 어기면 그 객체를 사용하는 다른 객체들이 어떻게 반응할 지 알 수 없다.

- **반사성(reflexivity)** : x.equals(x)는 true
- **대칭성(symmetry)** : x.equals(y)가 true이면 y.equals(x)도 true
- **추이성(transitivity)** : x.equals(y)는 true이고 y.equals(z)는 true이면 x.equals(z)는 true
- **일관성(consistency)** : x.equals(y)를 반복해서 호출해도 항상 true 또는 false를 반환
- **null-아님** : x.equals(null)는 false

<br/>

### 리스코프 치환 원칙

- 어떤 타입에 있어 중요한 속성이면 그 하위 타입에서도 중요하다.
- 그 타입의 모든 메서드가 하위 타입에도 똑같이 잘 작동해야 한다.
- Point의 하위 클래스는 여전히 Point이므로 어디서든 Point로써 활용될 수 있어야 한다.

<br/>

### equals 메서드를 잘 구현하는 방법

1. `==` 연산자를 사용해 입력이 자기 자신의 참조인지 확인한다.
2. `instanceof` 연산자로 올바른 타입의 값과 비교하는지 확인한다.
3. 올바른 타입으로 형변환 후, 비교를 원하는 핵심 값이 일치하는지 하나씩 검사한다.
4. `equals()`를 재정의 할 때에는 `hashCode()`도 반드시 재정의한다.

---

# hashCode()

> Override either both of them or neither of them.  
> (equals를 재정의한 클래스에는 hashCode도 반드시 재정의 한다.)

대체적으로 equals 비교에 사용되는 정보가 변경되지 않으면, 애플리케이션이 실행되는 동안 `hashCode()` 메서드는 항상 같은 값을 반환한다.

하지만 `equals()`가 두 객체를 다르다고 판단했다 한들, 두 객체의 `hashCode()` 값은 같을 수 있다.

해당 건을 [해시 충돌](https://preamtree.tistory.com/20) 이라고 한다. 좋은 해시 알고리즘은 서로 다른 인스턴스에 대해 다른 해시코드를 반환하는 것인데, 자세한 내용은 위 링크에서 읽어보자.

**문제가 되는 부분은 서로 같은 객체가 다른 해시값을 갖게 될 수 있다는 것이다.**

<br/>

## hashCode를 재정의 하지 않았을 경우 생기는 문제

- 서로 같은 객체가 다른 해시값을 갖게 될 수 있다

**특히나 HashMap의 key값으로 객체를 사용할 경우 문제가 발생한다.**

```java
@Test
void 같은_값을_가진_객체의_해시값_동일_확인() {
   //given
   Map<Item, Integer> items = new HashMap<>();

   items.put(new Item("연필", 500), 10);
   items.put(new Item("지우개", 300), 5);
   items.put(new Item("공책", 2000), 1);

   //when
   Item pencil = new Item("연필", 500);
   int amount = items.get(pencil);

   //then
   assertThat(amount).isEqualTo(10);
}
```

간단하게 `items`라는 `Map` 객체를 만든 후, 새로운 객체이지만 기존에 존재하는 값과 내부 값은 동일한 `pencil`이라는 객체로 물품의 개수를 받아오는 코드이다.

![image-20210926223648591](https://tva1.sinaimg.cn/large/008i3skNgy1guucxd4hruj60v80bgt9w02.jpg)

![아 내눈..) - YouTube](https://tva1.sinaimg.cn/large/008i3skNgy1guucy1sy44j60zk0k03za02.jpg)

`NPE가 발생하면서 실패한다.`

해당 이유는 `pencil` 객체에 대한 **해시값**을 `items`에서 찾을 수 없기 때문이다.

<br/>

**`HashMap`의 key값으로 `Item` 클래스를 사용하기 위해선 `Item` 클래스에 `hashCode()` 메서드를 재정의 해야 한다.**

그래야 동일한 값을 가진 객체들은 항상 같은 해시값을 갖게 된다.

```java
@Override
public int hashCode() {
   return Objects.hash(name, price);
}
```

위와 같이 `hashCode()`를 재정의 하여서, 같은 값을 가지는 객체는 같은 해시값을 갖게 된다.

![image-20210926224100849](https://tva1.sinaimg.cn/large/008i3skNgy1guud1qqsw5j60os082t9102.jpg)

---

# HashTable

`equals()`와 `hashCode()` 메서드를 이해하기 위해선 자바에서 `Hash`가 무엇인지, `HashTable`은 어떻게 작동되는 것인지에 대한 원리를 알아야 한다.

아까 위의 [해시 충돌](https://preamtree.tistory.com/20) 링크를 읽고 왔다면 어느정도 이해가 되겠지만, 간단하게 설명해 보자면..

<br/>

`HashTable`은 `Key, Value` 형태로 데이터를 저장한다. 이때 **_해시함수_** 를 이용하여 key값을 기준으로 고유한 식별 값인 **해시값**을 만들게 된다. 해당 해시값은 버킷(Bucket)에 저장된다.

하지만 `HashTable`의 크기는 한정적이기 때문에 서로 다른 객체이더라도 같은 해시값을 갖게 될 수도 있다. 그것을 **해시 충돌(hash Collisions)** 이라고 한다.

![Hash Table](https://tva1.sinaimg.cn/large/008i3skNgy1guud7kfr3bj61350u0die02.jpg)

위의 그림은 해시함수를 `key % 10` 으로 정의하고, 입력값은 문자로 넣어 이루어지는 해시 테이블의 예시이다. 각 문자는 아스키코드로써 숫자로 변환되고 해당 숫자를 해시함수를 돌려 나온 값을 인덱스로 잡아 bucket 내에서 linkedList로 관리된다.

또한 **같은 해시값의 버킷 안에 다른 객체가 있는 경우 `equals()` 메서드를 통해 구분된다.**

<br/>

- **HashTable에 put 메서드로 객체를 추가하는 경우**

  1.  값이 같은 객체가 이미 있다면(_equals()가 true_) 기존 객체를 덮어쓴다.
  2.  값이 같은 객체가 없다면(_equals()가 false_) 해당 entry를 LinkedList에 추가한다.

- **HashTable에 get 메서드로 객체를 조회하는 경우**
  1. 값이 같은 객체가 있다면 (_equals()가 true_) 그 객체를 리턴한다.
  2. 값이 같은 객체가 없다면(_equals()가 false_) null을 리턴한다.

**그리하여 위 그림의 index 5번의 값인 A, K, u 는 서로 같은 해시값을 갖지만, 고유 값이 다르기 때문에 서로간 구별이 가능한 것이다.**

---

## 그래서 왜 equals()와 hashCode()를 같이 정의해야 하죠?

만약 `equals()`를 재정의 하지 않는다면 값이 같을 경우를 확인하지 못해 제대로 된 값을 반환받지 못할 가능성이 크다.

만약 `hashCode()`를 재정의 하지 않는다면 같은 값 객체라도 해시값이 다를 수 있어 `HashTable`에서 객체가 저장된 버킷을 찾을 수 없는 경우가 생긴다.

---

# 결론

`equals()와 hashCode()는 항상 같이 재정의 하자!`

<br/>

---

### 출처

[Hash Table](https://better-dev.netlify.app/java/2020/01/03/hashtable/)

[[IT 기술면접 준비자료] 해시(Hash)와 해시충돌(Hash Collision)](https://preamtree.tistory.com/20)

[[Java] equals() & hascode() 메서드는 언제 재정의해야 할까?](https://velog.io/@sonypark/Java-equals-hascode-%EB%A9%94%EC%84%9C%EB%93%9C%EB%8A%94-%EC%96%B8%EC%A0%9C-%EC%9E%AC%EC%A0%95%EC%9D%98%ED%95%B4%EC%95%BC-%ED%95%A0%EA%B9%8C#java-equals)
