---
layout: post
title: Effective Java 02 - item 09 -
subtitle: ''
categories: language
tags: java
comments: false
---

## try-finally 보다는 try-with-resources를 사용하라

자바 7에서 추가된 `try-with-resources`는 자원을 효과적으로 회수할 수 있는 기법이다.

이는 `AutoCloseable`을 구현한 객체라면, try 구문이 끝날 때 `close`를 호출해서 알아서 자원을 호출해간다.

그러니 자원을 사용 후 반환해야하는 클래스가 필요하다면 `AutoCloseable`를 확장해 사용하도록 하자.

`try-with-resources`의 예시는 아래와 같다.

```java
static String firstLineOfFile(String path, String defaultVal) {
    try(BufferedReader br = new BufferedReader(new FileReader(path))) {
        return br.readLine();
    } catch (IOException e) {
        return defaultVal;
    }
}
```

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
