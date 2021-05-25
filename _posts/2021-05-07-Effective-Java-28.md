---
layout: post
title: Effective Java 05 - item 26 -
subtitle: ''
categories: language
tags: java
comments: false
---

## Raw 타입은 사용하지 말라

쉽게 이야기하면, 제네릭 타입으로 선언이 되었다면 가급적 Raw 타입의 사용은 자제해야한다는 뜻인데,

가령 제네릭이 없던 시절의 컬렉션의 경우 다음과 같이 선언했었다.

```java
private Collection stamps = ...;
```

만약 stamps의 타입이 `Stamp`이지만 `Coin`을 넣어도 컴파일 타임에는 오류를 발생시키지 않는다.

오류는 가능한 한 발생 즉시, 컴파일 타임에 잡아내는 것이 가장 좋다.

Raw Typedㅡㄴ 런타임에 예외가 발생할 수 있으니, 가급적 제네릭 타입을 사용하도록 하자.

`Set<Object>`와 `Set<?>`는 그나마 안전하지만 `Set`은 안전하지 않다.

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
