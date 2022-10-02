---
layout: post
title: '[Effective Java 뽀개기] 09. try-finally 보다는 try-with-resources를 사용하라'
subtitle: 'java'
date: 2022-10-02 23:23:29
categories: devlog
tags: java study
---

![image-20220904212556629](https://tva1.sinaimg.cn/large/e6c9d24egy1h5uufg0s7hj21do0ks0w4.jpg)

자바 라이브러리에서는 close 메서드를 호출해 직접 닫아줘야 하는 자원이 많다. 이러한 자원 닫기는 놓치기 쉬워 예측할 수 없는 성능문제로 이어지기도 한다.

_(ex) InputStream, OutputStream, java.sql.Connection 등_

<br/>

# 기존의 try-finally 방식

```java
static void copy(String src, String dst) throws IOException {
	InputStream in = new FileInputStream(src);

	try {
		OutputStream out = new FileOutputStream(dst);

		try {
			byte[] buf = new byte[BUFFER_SIZE];
			int n;
			while ((n = in.read(buf)) >= 0)
				out.write(buf, 0, n);
		}
	} finally {
		out.close();
	}
}
```

- **예외는 try 블록과 finally 블록 모두에서 발생할 수 있다.**
  - 가령 두 곳에서 모두 예외가 발생한다면 이후에 생긴 finally 블록의 예외가 try에서 생긴 예외를 집어 삼켜 첫번째 예외에 관한 정보가 남지 않게 되어 실 시스템의 디버깅을 어렵게 한다.

<br/>

# 자바 7 이후의 try-with-resources 방식

해당 방식 사용을 원한다면 `**AutoCloseable` 인터페이스를 구현해야 하는데, 이미 많은 클래스와 인터페이스가 구현하거나 확장해뒀다.\*\*

(혹시나 현재 읽고있는 사람도 차후에 닫아야 하는 자원을 뜻하는 클래스를 작성한다면 반드시 구현하자.)

```java
static void copy(String src, String dst) throws IOException {
	try (InputStream in = new FileInputStream(src);
				OutputStrem out = new FileOutputStream(dst)) {
			byte[] buf = new byte[BUFFER_SIZE];
			int n;
			while ((n = in.read(buf)) >= 0)
				out.write(buf, 0, n);
	}
}
```

- 읽기 수월하고, 문제를 진단하기도 훨씬 좋다.
- 숨겨진 예외들도 버려지지 않고 스택 추적 내역에 ‘숨겨졌다(suppressed)’는 꼬리표를 달고 출력된다.
  - `Throwable` 에 추가된 `getSuppressed` 메서드를 이용하면 프로그램 코드에서 가져올 수도 있다.

**두 방식 다 `catch` 절을 쓸 수 있다. `catch` 절을 사용하면 `try` 문을 중첩하지 않고도 다수의 예외를 처리할 수 있다.**

<br/>

# 정리

**꼭 회수해야 하는 자원을 다룰 때는 try-finally 대신 try-with-resources를 사용하자. (사실 무조건 사용하자)**
