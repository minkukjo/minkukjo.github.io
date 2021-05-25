---
layout: post
title: Effective Java 06 - item 35 -
subtitle: ''
categories: language
tags: java
comments: false
---

## ordinal 메서드 대신 인스턴스 필드를 사용하라

가급적이면 ordinal 메서드보다는 인스턴스 필드를 사용하는 편이 좋다.

그 이유는 상수 선언 순서를 바꾸는 순간 메소드가 제대로 동작하지 않기 때문에 ordinal() 메소드에 의존하기 보다는, 인스턴스 필드를 선언하여 사용하자.

```java
public enum Ensemble {
    SOLO, DUET, TRIO;

    public int numberOfMusicians() { return ordinal() + 1; }
}
```

위는 잘못된 코드이다.

아래처럼 사용하도록 하자.

```java
public enum Ensemble {
    SOLO(1), DUET(2), TRIO(3);

private final int numberOfMusicians;
    Ensemble(int size) {
        this.numberOfMusicians = size;
    }
}
```

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
