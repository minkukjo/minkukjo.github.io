---
layout: post
title: Effective Java 02 - item 08 -
subtitle: ''
categories: language
tags: java
comments: false
---

## finalizer와 cleaner 사용을 피하라

finalizer는 예측할 수 없고 상황에 따라 위험할 수 있어 사용하지않는 편이 좋다.

자바 9에서는 finalizer의 사용을 자제하며 (Deprecated) cleaner를 그 대안으로 했으나 이 역시도 좋지 않다.

cleaner는 여전히 예측할 수 없고, 느리며, 일반적으로 불필요하다.

자바에서는 메모리 자원의 회수를 위해 자바 7에서 제공하는 `try-with-resource`와 `try-finally`를 사용해 해결하는 편이 좋다.

그럼 이 finalizer와 cleaner는 도당체 언제 쓰인단 말인가?

첫 번째는, 클라이언트가 하지 않은 자원 회수를 늦게라도 해주기 위한 안전망의 역할로써 finalizer를 사용해볼 수 있다.

안전망으로써 finalizer를 사용하는 자바 라이브러리는 `FileInputStream`과 `FileOutputStream`, `ThraedPoolExecutor`가 있다.

두 번째는, 네이티브 피어(native peer)와 연결된 객체이다.

네이티브 피어란, 쉽게 말해 자바 객체가 아닌 (가비지 컬렉터의 대상이 되지 않는) 네이티브 메서드를 통해 기능을 위임받은 네이티브 객체를 의미한다.

이 때 자원을 회수하려는 목적으로 finalizer와 cleaner를 써볼 수 있는데, 이 역시도 네이티브 피어가 비싼 자원을 갖고 있지 않을 때에만 사용해볼 수 있다.

네이티브 피어가 사용하는 자원을 즉시 회수해야한다면 `close` 메소드를 사용하자.

즉, cleaner는 중요하지 않은 네이티브 피어 객체의 회수 용도로만 사용하자.

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
