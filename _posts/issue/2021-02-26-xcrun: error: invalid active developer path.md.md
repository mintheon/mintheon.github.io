---
layout: post
title: "자바스크립트 정규 표현식"
subtitle: ""
date: 2019-10-21 14:56:37
categories: devlog
tags: bit js web
---

정규 표현식 정리

---
1. 생성
  1. RegExp 생성자로 생성
    ```javascript
    var reg = new RegExp("abc");
    ```
  2. 정규 표현식 리터럴로 생성
    ```javascript
    var reg = /abc/;
    ```

2. `RegExp`객체의 메서드
  1. test
    - 정규 표현식 문자열이 일치하는지 뜻하는 논리값을 반환
    ```javascript
    var reg = /cat/;
    console.log(reg.test("cats and dogs")); //true
    console.log(reg.test("Cat")) //false
    ```
  2. exec
    - 정규 표현식과 일치하는 문자열을 검색하여 일치한 문자열을 배열로 반환
    - 반환된 배열에는 `index`와 `input` 프로퍼티가 있다. `index` 프로퍼티에는 가장 처음 일치한 위치가 들어가고 `input` 프로퍼티에는 일치한 문자열이 들어간다. 
    ```javascript
    var reg = /Script/;
    var result = reg.exec("JavaScript");
    console.log(result[0]); // -> "Script"
    ```

3. 문자클래스
  1. 문자클래스 : [...]
     1. [a-z] → 'a' ~ 'z' 중 문자 한개와 일치
     2. [abc] → 'a', 'b', 'c' 중 문자 한개와 일치
     3. [a-zA-Z0-9] → 모든 알파벳과 숫자 중 문자 한 개
     4. `/<h[1-6]>/` → HTML 제목 요소 <h1> ~ <h6> 와 일치
    ```javascript
    console.log(/[a-z]/.test("10 little indians")); // true
    ```
  2. 부정 문자 클래스 : [^...]
     1. [^0-9] → 숫자 외의 문자 한개

4. 문자 클래스의 단축 표기
   1. 임의의 문자 한개 : `.`
      1. `/c.t/` → "cat", "cute" 등과 일치
      2. `/c..l/` → "hot and cool" 등과 일치
   2.  숫자와 숫자 외의 문자 : `\d`, `\D`
    1. `\d` → `[0-9]`의 단축 표기
    2. `\D` → `[^0-9]`의 단축 표기
    ```javascript
    var dateTime = /\d\d\d\d-\d\d-\d\d \d\d:\d\d/;
    console.log(dateTime.test("it's 2016-09-27 10:15")); // true
    console.log(dateTime.test("2016-Aug-27 10:15")); // false
    ```

  2. 단어 문자와 단어 문자 외의 문자 : `\w`, `\W`
     1. `\w` → `[a-zA-Z0-9_]`의 단축 표기 (알파벳, 숫자, 언더스코어)
     2. `\w` → `[^a-zA-Z0-9_]`의 단축 표기
     ```javascript
     console.log(/\w/.test("A")); // true
     console.log(/\w/.test("!@#&%-")); // false
     ```
  
  3. 공백 문자와 공백 문자 외의 문자 : `\s`, `\S`
     1. `\s` → 공백 문자, 탭 문자, 개행 문자 등과 일치 (일본어에서만 사용하는 공백 문자인 전각 스페이스까지 포함)
     2. `\S` → 공백문자가 아닌문자와 일치
      ```javascript
      console.log(/\s\w\w/.exec("JavaScript RegExp")); // [" Re"] : " Re"와 일치
      console.log(/\s\w\w/.exec("JavaScriptRegExp")); // null
      ```

5. 반복 패턴
    1. 최소 m번, 최대 n번 반복 : `{m, n}`
      - 바로 앞의 요소를 최소 m번, 최대 n번 반복한다.  
        (`/[a-z]{6, 12}/` → 알파벳 소문자가 여섯 자 이상이며 열두 자 이하인 문자열과 일치)
    2. 바로 앞의 요소를 최소 n번 반복 : `{n,}`
      - `/[a-z]{6,}/` → 알파벳 소문자가 여섯 자 이상인 문자열과 일치
    3. 바로 앞의 요소를 n번 반복 : `{n}`
      ```javascript
      var dateTime = /\d{4}-\d{2}-\d{2} \d{2}:\d{2}/;
      console.log(dateTime.test("it's 2016-08-27 10:15")) // true
      console.log(dateTime.test("2016-Aug-27 10:15")); // false
      ```

등..
---

#### 요약정리 

![KakaoTalk_20191021_161254872](https://user-images.githubusercontent.com/23326757/67184094-ec779680-f41d-11e9-81e6-648133de2d2a.jpg)


