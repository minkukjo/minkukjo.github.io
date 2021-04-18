---
layout: post
title: Effective Java 02 - item 04 -
subtitle: ''
categories: language
tags: java
comments: false
---

## Private 생성자나 열거 타입으로 싱글턴임을 보증하라

싱글턴은 인스턴스를 오직 하나만 생성할 수 있는 클래스 생성 패턴을 말한다.

싱글턴 클래스의 단점은 **클라이언트가 테스트 하기가 어렵다**라는 점인데, 왜 그럴까?

그 이유는 인터페이스로 정의한 다음 그 인터페이스를 구현한 싱글턴 인스턴스가 아니라면, Mock 객체로 구현해 대체할 수 없기 떄문이다.

싱글턴 오브젝트 생성에는 여러 방법이 있지만, 이 책에서 가장 선호하는 싱글턴 오브젝트 방법은 원소가 하나인 열거 타입을 만드는 것이다.

아래와 같다.

```java
public enum Elvis {
    INSTANCE;

    public void leaveTheBuilding() { ... }
}
```

어찌보면 좀 어색해 보이지만, 대부분 상황에서 원소가 하나뿐인 열거타입이 싱글턴을 만드는 가장 좋은 방법이라고 저자는 소개하고 있다.

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
