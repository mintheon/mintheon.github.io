---
layout: post
title: '문자열 알고리즘 (Navie, Rabin-Karp, KMP)'
subtitle: '문자열 알고리즘'
date: 2021-12-19 03:15:00
categories: algorithm
tags: algorithm cs 글또
---

![image-20210815114036054](https://tva1.sinaimg.cn/large/008i3skNgy1gxiibhkaahj31e80l4wfz.jpg)

# 문자열 검색이란

**찾고자 하는 패턴의 문자를 내용에서 어디에 있는지 찾는 것.**

패턴과 일치하는 방식으로 찾는 방법은 같으나 효율적으로 찾는 알고리즘들이 여러개 있다.

## 종류

1. Naive(브루트포스 방식)
2. Rabin-Karp
3. KMP(Knuth-Morris-Pratt)

---

# 1. Naive

Navie 방식은 본문 **처음부터 끝까지 문자 하나하나씩 패턴과 비교**하여 찾는다.

해당 알고리즘은 효율적인 알고리즘이라고 할 수 없다. 매번 문자열을 비교할 때마다 얻게 되는 정보를 다음번 문자열 비교에서 사용하지 않기 때문이다. ~~다른 알고리즘을 쓰자!~~

## 동작방식

![img](https://tva1.sinaimg.cn/large/008i3skNgy1gx6mp4v65gg30lm04o74i.gif)

## 예시코드

```java
String text = "HELLO WORLD";
String pattern = "LO";

int textSize = text.length();
int patternSize = pattern.length();

for(int i = 0; i < textSize - patternSize; i++) {
	for(int j = 0; j < patternSize; j++) {
		if(text.charAt(i + j) != pattern.charAt(j)) {
			break;
		}

		if(j == m) {
			System.out.print("패턴을 찾았습니다. 해당 패턴의 시작 index는 %d 입니다.\n", i);
		}
	}
}
```

## 시간복잡도

O((N - M + 1) _ M) → \*\*O(N _ M)\*\*

---

# 2. Rabin-Karp

**해싱기법을 사용하여 문자열에서 특정 패턴과 일치하는지 찾아보는 알고리즘.**

Hash값이 겹칠 가능성이 있어 조금 위험해보이지만, 그런 경우는 거의 없다고 한다. 또한 겹치더라도 실제 맞는지 확인하는 작업을 거쳐 찾아낸다.

## naive와의 차이점

Naive Search와 같이 **모든 문자열을 탐색**하는 것은 맞다.

하지만 Naive Search의 경우 모든 경우에 내부 반복문을 수행하였다.

(ex) 10000글자가 있는 글을 탐색하며 100글자의 패턴 매치를 진행하며, 만약 앞쪽 99글자가 같고 1글자가 틀리면 해당 1글자가 틀리다는걸 모르기 때문에 100글자 모두 매칭을 진행함.

하지만 Rabin-Karp 알고리즘은 **내부 반복문을 돌기 전에 타겟(패턴을 찾을 문자열)과 패턴의 Hash값을 비교한 후, 값이 같을 경우에 패턴 매치를 진행**한다.

(해시 함수를 업데이트 하는 로직은 O(1)이다. → 이후에 설명함)

## 해시 함수([Rabin Fingerprint](https://en.wikipedia.org/wiki/Rabin_fingerprint))

HashCode는 아래와 같은 규칙으로 생성한다.

- **_"abcde"의 해시값 == 3033_**

![image-20211208195558519](https://tva1.sinaimg.cn/large/008i3skNgy1gx6mijeui4j30yk08wwf0.jpg)

하지만 해당 **해시함수를 생성하는 것은 O(N)이 걸린다.**

→ a, b, c, d, e 각각의 해시값을 구해 연산해야하기 때문

매번 O(N)으로 생성해야 하면 기존 Naive Search보다 훨씬 느릴것이다. 그래서 Rabin-Karp 알고리즘 **O(1)의 해시 업데이트 방식을 사용**한다.

![image-20211208195622155](https://tva1.sinaimg.cn/large/008i3skNgy1gx6miy0hxoj30z605k3yt.jpg)

"bcdef"의 경우, **((3033 - a의 해시값) \* 2) + f의 해시값** 으로 계산할 수 있겠다.

1. **기존 해시 - 제일 앞문자 해시**
2. **2 곱하기**
   ![image-20211208195644254](https://tva1.sinaimg.cn/large/008i3skNgy1gx6mjbj651j312606c74o.jpg)
3. **+ 제일 뒷문자 해시**
   ![image-20211208195701838](https://tva1.sinaimg.cn/large/008i3skNgy1gx6mjm9owwj30qs04gt8u.jpg)
4. **완성!**

### ✅ 추가

해당 알고리즘은 해시값을 계산할 때 오버플로우 방지를 위해 모듈러 연산을 사용하고 있다. 사실 오버플로우가 발생하더라도 해시값은 동일하다고 볼 수 있기때문에 사용하지 않아도 정상적으로 동작하기는 한다.

## 동작방식

1. **본문에서 패턴의 길이만큼 해시 값을 구한다.**
   ![image-20211208195723553](https://tva1.sinaimg.cn/large/008i3skNgy1gx6mjzufu7j309x079t8q.jpg)
2. **해시 값이 같지 않다면 한칸씩 이동하며 비교한다.**
   ![image-20211208195741043](https://tva1.sinaimg.cn/large/008i3skNgy1gx6mkby3lij311u07ngme.jpg)

## 예시코드

```java
String text = "HELLO WORLD";
String pattern = "LO";

int textSize = text.length();
int patternSize = pattern.length();

int textHash = hash(text, patternSize);
int patternHash = hash(pattern, patternSize);
int power = 1 << (patternSize - 1);

for(int i = 0; i < textSize - patternSize; i++) {
  //해시를 업데이트 해야할 경우
  if(i != 0) {
    //맨앞
    int frontValue = text.charAt(i - 1) * power;
    //맨뒤
    int backValue = text.charAt(i - 1 + patternSize);

    textHash = 2 * (textHash - frontValue) + backValue;
  }

  if(textHash == patternHash) {
    boolean isFind = true;

    for(int j = 0; j < patternSize; j++) {
      if(text.charAt(i + j) != pattern.charAt(j)) {
        isFind = false;
        break;
      }
    }

    if(isFind) {
      System.out.print("패턴을 찾았습니다. 해당 패턴의 시작 index는 %d 입니다.\n", i);
    }
  }
}

private int hash(String text, int length) {
  int hash = 0, power = 1;

  //뒤부터 2의 배수씩 곱해준다.
  for(int i = length - 1; i >= 0; i--) {
    hash += text.charAt(i) * power;
    power *= 2;
  }

  return hash;
}
```

## 시간복잡도

**O(M \* N)** → 최악일경우..

---

# 3. KMP

고급스러운 알고리즘일수록 이전에 검색한 정보를 잘 활용하는 편이다. 다들 KMP에서 어려워 하곤 한다.

앞전 알고리즘들과 달리, 매칭 시도중 실패했을 때 한칸 옆에서 다시 비교하는게 아닌, **여러칸을 건너 뛰어 다시 비교한다. 실패된 매칭이더라도 해당 정보를 버리지 않고 다음 매칭을 위해 사용하는 것이 핵심**이다.

## LPS 테이블 (Longest Prefix & Suffix)

일단은 처음엔 LPS 테이블 부터 만들게 되는데, LPS 테이블은 자기 자신을 제외한 동일한 **Prefix**와 **Suffix**쌍의 **최대 길이**를 적어 주면 된다. _(길이가 원본 문자열 대비 최소 1이라도 짧아야 한다.)_

### 예시 1 - "abaaba"

**Prefix(접두사)는 정방향에서부터, Suffix(접미사)는 역방향에서부터 읽으면 된다.**

원본 문자열보다 1 짧은 "abaab"로 예시를 들어보면 Prefix와 Suffix는 다음과 같다.

- **Prefix** - {a, **ab**, aba, abaa}
- **Suffix** - {b, **ab**, aab, baab}

이렇게 Prefix와 Suffix가 같은 경우가 "ab"이기 때문에 길이 2가 테이블의 값이 된다.

아래는 그렇게 만들어진 문자열마다의 테이블이다.

![image-20211208195806421](https://tva1.sinaimg.cn/large/008i3skNgy1gx6mkqjme0j312i0dg74z.jpg)

### 예시2 - "aaaaaa"

**Prefix와 Suffix가 겹치는 경우**이다. 이 경우에는 **동일한 원소중 제일 긴 원소의 길이로 테이블 값을 설정**한다.

"aaaa"의 경우 아래와 같다.

- **Prefix** - {a, aa, **aaa**}
- **Suffix** - {a, aa, **aaa**}

![image-20211208195818599](https://tva1.sinaimg.cn/large/008i3skNgy1gx6mkyiuyij312i0di0tg.jpg)

## 동작방식

KMP 알고리즘의 활용 예시로 유명한 DNA 염기서열 시퀀싱 문제로 설명한다. 여기선 G(구아닌), C(사이토신), A(아데닌), T(티민) 의 총 4개의 문자를 사용한다.

1. **가장 먼저 시퀀스의 'C'와 패턴의 'C'부터 비교한다.**
   ![image-20211208195837476](https://tva1.sinaimg.cn/large/008i3skNgy1gx6mla4jsxj311y0dgwfc.jpg)

0번(C)와 1번(T) 까지는 매칭되었으나 2번(C/G) 에서 매칭 되지 못했다. 이때 LPS테이블에서 가장 마지막 매칭에 성공한 인덱스(1번=T)의 값을 가져온다.

해당 값은 0이기 때문에, 틀린 위치(C/G)에서 0칸 뒤에서 비교하면 된다. 즉 **LPS 값이 0인경우 틀린 위치(C/G) 에서 다음 검사를 시작**하면 된다.

2. **틀렸던 'C' 위치에서 다시 매칭을 시작한다.**
   ![image-20211208195858020](https://tva1.sinaimg.cn/large/008i3skNgy1gx6mlo5aibj30zk0c50u0.jpg)

0번(C)는 맞았으나 1번(A/T)에서 매칭되지 못했다. 가장 마지막에 매칭되었던 인덱스(0번=C)의 값을 가져온다.

역시나 LPS값이 0이기 때문에 틀린 위치(A/T)에서 다시 검사를 시작한다.

3. **틀렸던 'A' 위치에서 다시 매칭을 시작한다.**
   ![image-20211208195921575](https://tva1.sinaimg.cn/large/008i3skNgy1gx6mm1zo88j30zk0jnac4.jpg)

이번엔 아예 처음부터 들렸다. 이런 경우에는 **문자열 자체가 매칭된 것이 하나도 없기 때문에 전체 문자열 시퀀스에서 한칸 옆에서 비교**하면 된다.

4. **한칸 옆 'C' 위치에서 매칭 시작한다.**
   ![image-20211208195936013](https://tva1.sinaimg.cn/large/008i3skNgy1gx6mmas883j30zk0cqwfp.jpg)

패턴 텍스트의 CTGCCT까지 맞추고 A에서 매칭 실패했다. 그래서 마지막으로 매칭된 5번(T)의 LPS 값이 2인 것을 확인한다. **LPS 값이 2라는 소리는 현재 틀린 위치(G/A) 이전의 2칸(C,T)는 패턴 문자열의 Prefix 2칸(C, T)와 동일하다는 말**이다.

그리하여 **틀린 위치(G/A)에서 2칸 뒤쪽의 'C' 위치에 패턴 문자열을 대고 C, T는 이미 맞으니 G부터 매칭을 시작하면 된다.**

5. **틀렸던 'G' 위치에서 뒤로 두칸 쪽에 패턴문자열을 대고 틀린 위치부터 다시 매칭 시작한다.**
   ![image-20211208195953090](https://tva1.sinaimg.cn/large/008i3skNgy1gx6mmljgesj30zk0ixjtc.jpg)

위에서 말했던 대로 이미 C, T는 맞는 것을 확인했기 때문에 G부터 검사를 시작한다.

6. **매칭 완료!**

## 예시코드

```java
String text = "CTCACTGCCTGCCTAG";
String pattern = "CTGCCTAG";

int textSize = text.length();
int patternSize = pattern.length();

char[] tx = text.toCharArray();
char[] pt = pattern.toCharArray();

//문자열 전체 인덱스(i)로 문자열 순회
for(int i = 0, j = 0; i < textSize; i++) {
  while (j > 0 && tx[i] != pt[j]) {
    j = table[j - 1];
  }

  if(tx[i] == pt[j]) {
    if(j == patternSize - 1) {
      System.out.print("패턴을 찾았습니다. 해당 패턴의 시작 index는 %d 입니다.\n", i - j);
      j = table[j];
    } else {
      j++;
    }
  }
}

private int[] makeTable(String pattern) {
  int size = pattern.length();
  char[] texts = pattern.toCharArray();
  int[] table = new int[size];

  for(int i = 1, j = 0; i < size; i++) {
    while (j > 0 && texts[i] != texts[j]) {
      j = table[j - 1];
    }

    if(texts[i] == texts[j]) {
      table[i] = ++j;
    }
  }

  return table;
}
```

위의 코드로 잘 실행되지만, **_j = table[j - 1];_** 부분을 왜 j를 0으로 초기화시키지 않는지 궁금할 수 있다.

![image-20211208200017419](https://tva1.sinaimg.cn/large/008i3skNgy1gx6mn0lq49j30zk0b0wfn.jpg)

**_j = 0으로 초기화할 경우_**, **포개져있는 패턴을 발견할 수 없다.**

![image-20211208200031053](https://tva1.sinaimg.cn/large/008i3skNgy1gx6mn9fbe2j30zk0c6jtc.jpg)

**j = table[j] 로 초기화** 했을 경우, 찾아낸 마지막 **C글자와 포개지는 똑같은 패턴이 있다면 패턴을 찾아낼 수 있다.**

두번째로, **LPS 테이블 생성함수는 놀랍게도 KMP 알고리즘과 매우 유사**하다. 해당 부분에 대한 정확한 시뮬레이션은 아래 나동빈님의 동영상을 참고.

**사실상 KMP 알고리즘으로 LPS 테이블을 생성하는 것**이다.

## 시간복잡도

**O(N + M)**

## 추가 자료

강력추천(나동빈님) - **[34강 - KMP 문자열 매칭 알고리즘 [ 실전 알고리즘 강좌(Algorithm Programming Tutorial) #34 ]](https://www.youtube.com/watch?v=yWWbLrV4PZ8)**

[문자열 알고리즘 (3) - KMP 알고리즘](https://gusdnd852.tistory.com/172?category=748315)

[KMP algorithm for string matching - part 2](https://www.youtube.com/watch?v=q-SMEUwcnJI)

---

# 출처

[Swift Algorithm Club: Boyer Moore String Search Algorithm](https://www.raywenderlich.com/541-swift-algorithm-club-boyer-moore-string-search-algorithm#toc-anchor-002)

[[18-알고리즘] 문자열 탐색(String Match) 소개 - Naive string match](https://covenant.tistory.com/81)

[문자열 알고리즘 (3) - KMP 알고리즘](https://gusdnd852.tistory.com/172?category=748315)

[[18-알고리즘] 문자열 탐색(String Match) 라빈 카프 알고리즘(Rabin-Karp algorigm)](https://covenant.tistory.com/82?category=727176)

[Rabin-karp(라빈 카프) 알고리즘](https://junstar92.tistory.com/125)

[문자열 알고리즘 (3) - KMP 알고리즘](https://gusdnd852.tistory.com/172?category=748315)

[문자열 검색 알고리즘 1편 (Naive, Rabin Karp, KMP)](https://izmirprogramming.tistory.com/8)

[문자열 알고리즘](https://namu.wiki/w/%EB%AC%B8%EC%9E%90%EC%97%B4%20%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98)

[[알고리즘]문자열 검색(고지식한 검색, 라빈-카프, KMP, 보이어-무어)](https://otrodevym.tistory.com/entry/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98%EB%AC%B8%EC%9E%90%EC%97%B4-%EA%B2%80%EC%83%89%EA%B3%A0%EC%A7%80%EC%8B%9D%ED%95%9C-%EA%B2%80%EC%83%89-%EB%9D%BC%EB%B9%88%EC%B9%B4%ED%94%84-KMP-%EB%B3%B4%EC%9D%B4%EC%96%B4%EB%AC%B4%EC%96%B4)

[[알고리즘] String-searching Algorithm](https://velog.io/@djh20/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-String-searching-Algorithm)
