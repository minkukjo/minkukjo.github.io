---
layout: post
title: Effective Java 04 - item 22 -
subtitle: ''
categories: language
tags: java
comments: false
---

## 인터페이스는 타입을 정의하는 용도로만 사용하라

클래스가 어떤 인터페이스를 구현한다는 것은 자신의 인스턴스로 무엇을 할 수 있는지를 클라이언트에게 얘기해주는 용도이다.

인터페이스는 오직 이 용도로만 사용해야 한다.

이 지침에 맞지 않는 예로는 상수 인터페이스라는 것이 있는데, 상수 인터페이스란 메서드 없이, 상수를 뜻하는 static final 필드로만 가득 찬 인터페이스를 말한다.

상수 인터페이스 안티패턴은 인터페이스를 잘못 사용한 예이다.

인터페이스는 타입을 정의하는 용도로만 사용하자. 상수 공개용 수단으로 사용해서는 안된다!

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
