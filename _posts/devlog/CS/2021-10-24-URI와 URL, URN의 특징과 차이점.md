---
layout: post
title: "URI와 URL, URN의 특징과 차이점"
subtitle: "cs"
date: 2021-10-24 20:38:00
categories: devlog, cs
tags: 글또
---
![CS Banner](https://tva1.sinaimg.cn/large/008i3skNgy1gvqn9h39vcj615o0g1gol02.jpg)

예전에 다른 개발자와 Request 얘기를 나누다가 `URL` 이라고 얘기했는데.. 말을 마치고 나니 **_'엥 이게 `URL`이 맞나? `URI`인가?'_** 라고 헷갈린 적이 있었다.

그리하여 정확한 차이를 정리해보려고 한다. 추가적으로 찾다보니 알게된 URN도 함께 알아본다.

---

![도식화](https://tva1.sinaimg.cn/large/008i3skNgy1gvqm8uosnwj60u00u03zq02.jpg)

![img](https://tva1.sinaimg.cn/large/008i3skNgy1gvqm98vtq7j60bj06m0sv02.jpg)

각 바운더리는 이러하다. 한눈에 각 바운더리를 알 수 있다.

---

# URI

**U**niform **R**esource **I**dentifier의 약자이다.

통합 자원 식별자라고 부르며 웹에서 사용하는 **논리적 또는 물리적 리소스를 식별하는 고유한 문자열**이다.

**URL과 URN을 포함**한다.

# URL

**U**niform **R**esource **L**ocator의 약자이다.

흔히 웹 주소라고 하며, 네트워크 상에서 리소스가 어디있는지 알려주기 위한 규약이다.

- 프로토콜 포함 O
- **해당 자원의 위치(Path)를 의미**한다.
- 일반적으로 웹 사이트의 도메인을 의미한다.
- 웹 뿐만 아니라 컴퓨터 네트워크상의 자원은 모두 나타낼 수 있다.

# URN

**U**niformed **R**esource **N**ame의 약자이다.

주소상 파라미터 부분까지 포함된다.

- 프로토콜 포함 X
- **해당 자원의 이름을 의미**한다.
- 독립적인 자원 지시자이다.
- Page 이후 부분까지 모두 포함한다.

---

**요새는 `URI`라는 말을 더 많이 쓰는데 왜 그럴까?**

옛날에는 웹사이트 주소가 http://www.mintheon.com/company/location.jsp 식의 주소를 많이 썼다. 

그런데 요새는 어떤가? http://www.mintheon.com/company/location 와 같은 주소를 많이 쓴다. 

무엇이 다른가? 뒤쪽의 확장자가 없다. 

대체적으로 예전에는 웹사이트의 리소스를 불러오는형식(.jsp파일) 즉, **예전에는 위치를 호출하는 방식을 많이 사용**했다. 하지만 Restful을 추구하게 되면서 페이지를 단어로써 구분할 수 있도록 **최근에는 구분자로 데이터를 호출해오는 방식을 사용**한다.

실제로 해당 사이트에는 company/location이라는 파일은 없다. 실제로 Controller에서 company/location으로 정의된 메서드를 호출할것이다. **이렇게 구분자(Identifier)로 보는것이 `URI`**이다.

기존에는 해당 리소스의 위치를 통해 불러왔기 때문에 `URL`이라는 단어를 훨씬 많이 사용했던 것이다.

즉, **`URL`은 해당 데이터의 위치를 불러오고 `URI`는 해당 데이터의 구별자를 통해 데이터를 불러온다.**

그렇다면 `URN`은 무엇인가? URN은 조금 생소할 수도 있지만 AWS를 사용하는 사람은 한번쯤 봤을 수 있다.

**`URN` 문법은 `isbn`이나 `uuid`를 해석 가능한 프로그램이 있어야 동작**한다.

- urn:isbn:0451450523
- urn:uuid:6e8bc430-9c3a-11d9-9669-0800200c9a66

예를 들면 이런 주소이다. AWS를 사용해봤다면 자원의 위치를 이런 **`URN`을 확장한 `ARN`**으로 제공한다.

- arn:aws:kms:ap-northeast-2:888869365995:key/679f76c9-a93d-4144-8163-9319e9d9cd18

이런 형태의 주소를 받은 적이 있을것이다. 처음에 이게 도대체 이게 무슨 주소지 했는데 ARN을 사용한 주소라고 한다!

해석하자면 _arn문법_으로 이루어졌으며 _aws의 kms 서비스_이고, _ap-northeast-2(서울) 지역_의 _888869365995 사용자_의 _key/679f76c9-a93d-4144-8163-9319e9d9cd18 리소스_를 말하는 것이다.


**`이제 더 이상은 URI, URL, URN의 개념을 까먹지 않겠지!!`**

---

#### 출처

[[네트워크📶] URI 란 ? / URI VS URL VS URN 차이 /](https://programming119.tistory.com/194)

[URI란? (feat. URL, URN)](https://www.howdy-mj.me/network/what-is-uri/)

[URL과 URI의 의미와 차이점 (Difference between URL & URI)](https://blog.lael.be/post/61)
