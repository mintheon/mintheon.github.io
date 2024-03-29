---
layout: post
title: "URI와 URL, URN의 특징과 차이점"
subtitle: "cs"
date: 2021-10-24 20:38:00
categories: devlog
tags: 글또 cs
---
![CS Banner](https://tva1.sinaimg.cn/large/008i3skNgy1gvqn9h39vcj615o0g1gol02.jpg)

예전에 다른 개발자와 Request 얘기를 나누다가 `URL` 이라고 얘기했는데.. 말을 마치고 나니 **_'엥 이게 `URL`이 맞나? `URI`인가?'_** 라고 헷갈린 적이 있었다.

![img](https://tva1.sinaimg.cn/large/008i3skNgy1gvqonmmqzoj60dw0l0dhe02.jpg)

그리하여 정확한 차이를 정리해보려고 한다. 추가적으로 찾다보니 알게된 `URN`도 함께 알아본다.

---

![도식화](https://tva1.sinaimg.cn/large/008i3skNgy1gvqm8uosnwj60u00u03zq02.jpg)

![img](https://tva1.sinaimg.cn/large/008i3skNgy1gvqm98vtq7j60bj06m0sv02.jpg)

각 바운더리는 이러하다. 한눈에 각 바운더리를 알 수 있다.

---

# URI

**_U_**niform **_R_**esource **_I_**dentifier의 약자이다.

통합 자원 식별자라고 부르며 웹에서 사용하는 **_논리적 또는 물리적 리소스를 식별하는 고유한 문자열_**이다.

**_URL과 URN을 포함_**한다.

# URL

**_U_**niform **_R_**esource **_L_**ocator의 약자이다.

흔히 웹 주소라고 하며, 네트워크 상에서 리소스가 어디있는지 알려주기 위한 규약이다.

- 프로토콜 포함 O
- **_해당 자원의 위치(Path)를 의미_**한다.
- 일반적으로 웹 사이트의 도메인을 의미한다.
- 웹 뿐만 아니라 컴퓨터 네트워크상의 자원은 모두 나타낼 수 있다.

# URN

**_U_**niformed **_R_**esource **_N_**ame의 약자이다.

주소상 파라미터 부분까지 포함된다.

- 프로토콜 포함 X
- **_해당 자원의 이름을 의미_**한다.
- 독립적인 자원 지시자이다.
- Page 이후 부분까지 모두 포함한다.

---

## **요새는 `URI`라는 말을 더 많이 쓰는데 왜 그럴까?**

옛날에는 웹사이트 주소를 _`http://www.mintheon.com/company/location.jsp`_ 식의 주소를 많이 썼다.

그런데 요새는 어떤가? _`http://www.mintheon.com/company/location`_ 와 같은 주소를 많이 쓴다. 

<br/>

무엇이 다른가? **_뒤쪽의 확장자가 없다._**

대체적으로 예전에는 웹사이트의 리소스를 불러오는 형식(.jsp파일) 즉, **_예전에는 위치를 호출하는 방식을 많이 사용_**했다. 하지만 Restful을 추구하게 되면서 페이지를 단어로써 구분할 수 있도록 **_최근에는 구분자로 데이터를 호출해오는 방식을 사용_**한다.

<br/>

실제로 해당 사이트에는 company/location이라는 파일은 없다. 실제로 Controller에서 company/location으로 정의된 메서드를 호출할것이다. **_이렇게 구분자(Identifier)로 보는것이 `URI`_**이다.

![URLURI](https://www.charlezz.com/wordpress/wp-content/uploads/2021/03/www.charlezz.com-uri-url-uri-vs-url-768x240.png)

두 주소는 모두 index.html을 가리키고 있다.

첫번째 주소는 웹서버의 실제 파일 위치를 나타내는 주소이므로 URI이면서 URL이다.
두번째 주소는 실제로 index라는 파일이 웹서버에 존재하지 않으므로 URL은 아니다. 하지만 서버 내부에서 이를 처리하여 결국 index.html을 가리키기 때문에 URI라고 볼 수 있다.

기존에는 해당 리소스의 위치를 통해 불러왔기 때문에 `URL`이라는 단어를 훨씬 많이 사용했던 것이다. 즉, **_`URL`은 해당 데이터의 위치를 불러오고 `URI`는 해당 데이터의 구별자를 통해 데이터를 불러온다._**

<br/>

그렇다면 `URN`은 무엇인가? URN은 조금 생소할 수도 있지만 AWS를 사용하는 사람은 한번쯤 봤을 수 있다. **_`URN` 문법은 `isbn`이나 `uuid`를 해석 가능한 프로그램이 있어야 동작_**한다.

- urn:isbn:0451450523
- urn:uuid:6e8bc430-9c3a-11d9-9669-0800200c9a66

예를 들면 이런 주소이다. 

<br/>

## AWS에서 받는 주소는 도대체 무엇일까?

AWS를 사용해봤다면 AWS 자원의 위치를 이런 **_`URN`을 확장한 `ARN`_**으로 제공한다는 것을 알 것이다.

- arn:aws:kms:ap-northeast-2:888869365995:key/679f76c9-a93d-4144-8163-9319e9d9cd18

![img](https://tva1.sinaimg.cn/large/008i3skNgy1gvqop486adj60gk0bnt9z02.jpg)

처음에 이게 도대체 이게 무슨 주소지 했는데 ARN을 사용한 주소라고 한다!

해석하자면 **arn문법**으로 이루어졌으며 **aws의 kms 서비스**이고, **p-northeast-2(서울) 지역**의 **888869365995 사용자**의 **key/679f76c9-a93d-4144-8163-9319e9d9cd18 리소스**를 말하는 것이다.


**`이제 더 이상은 URI, URL, URN의 개념을 까먹지 않겠지!!`**

---

#### 출처

[[네트워크📶] URI 란 ? / URI VS URL VS URN 차이 /](https://programming119.tistory.com/194)

[URI란? (feat. URL, URN)](https://www.howdy-mj.me/network/what-is-uri/)

[URL과 URI의 의미와 차이점 (Difference between URL & URI)](https://blog.lael.be/post/61)
