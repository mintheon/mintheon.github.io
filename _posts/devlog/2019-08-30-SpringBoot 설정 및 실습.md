---
layout: post
title:  "SpringBoot 환경설정 및 예제"
subtitle:   ""
date: 2019-08-30 15:52:57
categories: devlog
tags: bit spring
---

SpringBoot를 이용하여 React와 통신하는 예제를 실습하였다.

---

<br/>
### Java 컴파일러 설정 (VSCODE 기준)

---

1. `Ctrl + ,` 키 눌러서 설정에 들어간다.
2. 설정의 우측 상단에 있는 아이콘(마우스를 위에 올리면 json 설정열기라고 나옴)을 눌러서 json 설정 파일에 들어가준다.
3. json 설정에서 `"java.home"`의 경로를 잡아준다.
4. 일단 본인의 JDK 경로를 확인해야하는데, 대부분 `C:\Program Files\Java\` 내에 있다.
5. 확인했으면 `json 설정파일`에 이렇게 적어준다.
   `"java.home": "본인의 jdk 경로"`

   아래는 본인의 경로이다.
   ```json
   (...)
   "java.home": "C:\\Program Files\\Java\\jdk1.8.0_221"
   (...)
   ```

---

<br/>
### Springboot 환경 설정 (VSCODE 기준)

---

1. `Springboot`를 환경설정하기 위한 확장 프로그램 추가
   [Spring Boot Extension Pack](https://marketplace.visualstudio.com/items?itemName=Pivotal.vscode-boot-dev-pack)
   [Spring Boot Tools](https://marketplace.visualstudio.com/items?itemName=Pivotal.vscode-spring-boot)
2. `F1`키를 누르고 `Spring init`을 치면 리스트가 주르륵 나온다. 거기서 `Maven Project`로 진입
3. 처음에 패키지 정해주는 부분에서 `com.bit` 작성 (뒤의 bit부분은 회사에 따라 변경 가능)
4. 두번째 창은 아키텍쳐 이름을 정해주는 부분인데 `back`이라고 적어줌.
   (여기도 임의로 작성 가능)
5. 버전은 2.1.7버전으로 선택하며, dependency는 추가하지 않는 옵션으로 선택
6. 폴더가 생성되면 프로젝트 내에 있는 `pom.xml` 파일을 설정해준다.
   (**P**roject **O**bject **M**odel → **pom**)
7. `pom.xml`파일의 test부분과 build 버전을 지우고 자바 버전은 자동으로 잡아주니 자바 버전 적힌 프로퍼티는 지운다.
   ##### pom.xml 파일
    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.1.7.RELEASE</version>
      <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.bit</groupId>
    <artifactId>back</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>demo</name>
    <description>Demo project for Spring Boot</description>
    
    <dependencies>
      <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-web</artifactId>
          </dependency>
    </dependencies>
    </project>
    ```
8. 다음으로 `src`안의 `test폴더`를 지워준다. 그리고 프로젝트 내에 `com 폴더`도 필요없으니 지운다.  
   (해당 예제에선 쓰지 않을 예정이라서)
9.  `src`안에 `app 폴더`를 만들고, `main 폴더` 안에 있는 `DemoApplication.java`를 `app폴더`에 넣는다.
10. `DemoApplication.java`의 패키지를 `package app;`으로 변경하고, 클래스 이름과 파일 이름도 보기좋게 `Application`으로 변경
  ##### Application.java 파일
  ```java
  package app;

  import org.springframework.boot.SpringApplication;
  import org.springframework.boot.autoconfigure.SpringBootApplication;

  // 코드에서 무조건 이 구문이 마지막에 있어야함. (스프링 디렉토리에 제일 상위에 있어야 함)
  @SpringBootApplication
  public class Application {

    public static void main(String[] args) {
      SpringApplication.run(Application.class, args);
    }

  }
  ```
11. 해당 프로젝트에서 `F5`로 실행하면 `launch.json`파일이 뜬다.
12. 해당 파일의 `configurations` 안에 `"console": "externalTerminal"`을 추가적으로 적어준다. (보기 좋게 외부 콘솔로 띄우기 위해서)
13. `Application.java` 파일에서 `Ctrl+F5`눌러 실행하여 콘솔 창 뜨는것 확인
14. 확인되면 `pom.xml`에서 `<dependencies>` 부분에 아래 코드 붙여넣기
  ```xml
  <!-- 스타터킷 받아주는 부분 -->
		<dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <!-- 스프링 부트가 꺼지지 않게 해주는 부분 -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-devtools</artifactId>
      <scope>runtime</scope>
      <optional>true</optional>
    </dependency>
  ```
15. 그리고 `<dependencies>` 끝에 build용 코드를 더 붙여준다.
  ```
  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
  ```
16. 이후에 다시 `Application.java`에서 실행해준다.

---

<br/>
### BackEnd 부분 코드 작성 (VSCODE 기준)

---

컨트롤러를 만들어 주는 작업을 해줄것이다. 라우터 개념과 비슷한데 통신할 수 있는 문을 만들어 주는 작업을 할 것이다.

1. `app폴더` 아래에 `controller폴더`를 만들어 준다.
2. `controller폴더`안에 `MathController.java`를 만듬
3. `class`를 입력하여 snippet으로 자동으로 만들게 한 다음 위에 어노테이션으로 `@RestController`를 적어준다. (스니펫으로 적어줄 경우 알아서 import도 다 해준다. **지금것과 이후 것들 모두 다 스니펫 사용하는것을 권장**)
4. `localhost:8080/math`로 들어오는 문을 열어주기 위해서 `@RequestMapping("/math")` 구문도 적어준다.
5. Class 안에서 `pumd`를 적어주면 스니펫으로 밑에 뜨는데 엔터를 눌러 메소드를 만들어준다. 
   (`public_method`라는 이름이며 public 타입의 함수를 만들어주는 스니펫이다.)
6. 위쪽에 `@CrossOrigin("*")`을 적어준다. 해당 구문은 모든 접근을 허용하게 만들어 준다.
7. i와 j값을 받아 더해주는 로직을 짤 예정이다. 아래 코드를 참조하자.
  ##### MathController.java 파일
  ```java
  package app.controller;

  import org.springframework.web.bind.annotation.CrossOrigin;
  import org.springframework.web.bind.annotation.GetMapping;
  import org.springframework.web.bind.annotation.PathVariable;
  import org.springframework.web.bind.annotation.RequestMapping;
  import org.springframework.web.bind.annotation.RequestParam;
  import org.springframework.web.bind.annotation.RestController;

  // REST API를 허용할 수 있는 어노테이션이다. 접속을 허용하는 구문
  @RestController

  // 모든 접근을 허용한다
  @CrossOrigin("*")

  // 요청을 이렇게 들어오게 해라 라고 정의해주는 구문 
  @RequestMapping("/math")
  public class MathController {

      // 23번: 주소창에 /math/를 뒤에 치면 해당 메소드 실행
      // Post 쓰고싶으면 PostMapping 쓰면 됨.
      @GetMapping("/")
      public String Hello() {
          return "안녕!";
      }
      
      // URI로 받는것 
      // 주소창에 http://localhost:8080/math/add/1/3 을 뒤에 치면 해당 메소드 실행됨
      // 1이랑 3을 받아서 4가 나옴.
      @GetMapping("/add/{i}/{j}")
      public int add(@PathVariable int i, @PathVariable int j) {
          return i + j;
      }

      // 쿼리로 받는거
      // http://localhost:8080/math/minus?i=1&j=3 적어주면 됨.
      // -2 나옴.
      @GetMapping("/minus")
      public int minus(@RequestParam("i") int i, @RequestParam("j") int j) {
          return i - j;
      }

      //requestbody로 하는 방식 있는데 이건 알아서 알아보기.
  }
  ```

---

<br/>
### frontEnd 부분 코드 작성 (VSCODE 기준)

---

1. `create-react-app front` 명령으로 원하는 위치에 폴더를 만들어 준다.
2. `src폴더`에 있는 `app.js`와 `index.js`를 제외한 파일 모두 삭제해준다. (안해줘도 되는데 깔끔해보임. 대신 삭제시 `index.js`파일 재설정 해줘야한다.)
3. `app.js`내의 내용을 모두 지우고 `rcc`를 입력해 스니펫으로 새로 클래스를 만들어 준다.
4. 이 글을 볼 때쯤이면 `index.js` 설정하는 법도 까먹을 테니 코드 올려본다.
   ##### index.js 파일
    ```
    import React from 'react';
    import ReactDOM from 'react-dom';
    import App from './App';

    ReactDOM.render(<App />, document.getElementById('root'));
    ```
5. `npm install axios`로 `Axios`를 깔아준다.
6. `package.json`에서 **proxy 설정**을 해준다.
  ```json
  (...)
  "private": true,
  "proxy": "http://localhost:8080/", // <= 이부분
  "dependencies": {
  "axios": "^0.19.0",
  (...)
  ```
7. `app.js` 에서 덧셈, 뺏셈 로직을 구현해준다. 여기는 소스를 첨부한다.
   ##### front 부분의 app.js 파일
    ```JSX
    import React, { Component } from 'react';
    import axios from 'axios'

    class App extends Component {

      state = {
        i: 0,
        j: 0,
        result: 0,
      }

      // 덧셈 통신부분
      handleAdd = async () => {
        let {i, j} = this.state;
        let res = await axios.get(`math/add/${i}/${j}`)

        this.setState({result: res.data})
      }

      // 뺄셈 통신부분
      handleMinus = async () => {
        let {i, j} = this.state;
        let res = await axios.get(`math/minus?i=${i}&j=${j}`)

        this.setState({result: res.data})
      }

      // i값 변경 부분
      handleOnChangeI = async (e) => {
        this.setState({
          i: e.target.value,
        })
      }

      // j값 변경 부분
      handleOnChangeJ = async (e) => {
        this.setState({
          j: e.target.value,
        })
      }

      render() {
        const {i, j ,result} = this.state;
        const {handleOnChangeI, handleOnChangeJ, handleAdd, handleMinus} = this

        return (
          <div>
            <div>
              <p>i = <input value = {i} onChange = {handleOnChangeI}></input></p>
              <p>j = <input value = {j} onChange = {handleOnChangeJ}></input></p>
              <button onClick = {handleAdd}>+</button>
              <button onClick = {handleMinus}>-</button>
              <p>{result}</p>
            </div>
          </div>
        );
      }
    }

    export default App;
    ```
8. 마지막으로 백단에서 `ctrl + F5`로 서버를 실행해주고, 프론트 단에서 `npm start`를 적어 실행하여 값이 잘 나오는 것을 볼 수 있다.
---