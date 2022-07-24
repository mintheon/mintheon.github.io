---
layout: post
title: 'Gradle Could not find method compile() 오류 해결'
subtitle: ''
date: 2022-07-24 16:07:29
categories: devlog
tags: gradle 글또 issue
---

샤딩 스피어 샘플코드를 만든다고 생각 없이 의존성을 붙여넣기 하던 와중 아래와 같은 오류가 발생했다.

```
Could not find method compile() for arguments [org.postgresql:postgresql:42.3.6] on object of type org.gradle.api.internal.artifacts.dsl.dependencies.DefaultDependencyHandler.
```

![image-20220724193649493](https://tva1.sinaimg.cn/large/e6c9d24egy1h4i78utclaj20go092t9b.jpg)

실행 없이 이것저것 작성한 상태에서 해당 오류를 본터라 어디가 문제지 하고 찾아보던 와중.. _아차..!_

<br/>

# 원인

문제는 아래의 구문이었다.

```
...
  compile 'org.postgresql:postgresql:42.3.6'
...
```

샤딩스피어에서 h2가 2.x 버전대가 사용이 불가능하여 부랴부랴 postgreSQL로 바꾸었는데, **생각없이 드라이버를 붙여넣기 하다가 `compile` 로 적어버린 것**이다.

<br/>

## 왜 이런일이?

**Gradle 4.10(2018.08.27) 버전 이후로 키워드가 변경되어 아래와 같이 대체되었다.**

| Gradle 4.10 이전 | Gradle 4.10 이후   |
| ---------------- | ------------------ |
| compile          | implementation     |
| runtime          | runtimeOnly        |
| testCompile      | testImplementation |
| testRuntime      | testRuntimeOnly    |

추가적으로 **Gradle 7.0(2021.04.09) 이전까지는 그나마 Deprecate 되어 두개를 혼용하여 사용했어도 괜찮았으나, 7.0 버전 이후엔 Gradle 4.10 이전의 키워드는 아예 삭제**되었다.

현재 본인이 사용하고 있는 Gradle 버전은 7.5 버전이기에 당연히 오류가 나는 것이었다.

<br/>

# 해결

**`compile` -> `implementation` 으로 변경하여 해결**하였다.
