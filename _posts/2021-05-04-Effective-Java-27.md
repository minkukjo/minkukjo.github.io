---
layout: post
title: Effective Java 05 - item 26 -
subtitle: ''
categories: language
tags: java
comments: false
---

## 제네릭

제네릭은 자바 5부터 지원하고 있다.

제네릭을 사용하기 전에는 컬렉션에서 객체를 꺼낼 때 마다 형변환을 해야했다.

그러나 제네릭을 사용함으로써 컬렉션이 담을 수 있는 타입을 컴파일러에게 알려주어 더욱 더 안전한 프로그래밍이 가능하게 되었다.

꼭 컬렉션이 아니더라도 이러한 이점을 누릴 수 있으나, 코드의 복잡성이 올라간다는 단점이 존재한다.

이번 장은 제네릭의 이점을 극대화하고 단점을 줄일 수 있는 방법에 대해서 공부해본다.

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
