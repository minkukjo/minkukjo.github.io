---
layout: post
title: Effective Java 06 - item 38 -
subtitle: ''
categories: language
tags: java
comments: false
---

## 확장할 수 있는 열거 타입이 필요하다면 인터페이스를 사용하라

인터페이스를 사용하여 확장 가능 열거 타입을 만들 수 있다.

```java
public interface Operation {
    double apply(double x, double y);
}

public enum BasicOperation implements Operation {
    PLUS("+") {
        public double apply(double x, double y) {
            return x + y;
        }
    }
    MINUS("+") {
        public double apply(double x, double y) {
            return x - y;
        }
    }
    ...
}
```

이처럼 이넘 타입은 인터페이스를 구현하여 확장이 가능하다.

이 확장 가능한 이넘 타입을 흉내내는 방식에도 한 가지 문제가 있으니, 바로 이넘 타입끼리는 구현을 상속할 수 없다는 점이다.

이넘 타입 자체는 확장이 어렵지만, 인터페이스와 그 인터페이스를 구현하는 이넘 타입을 함께 사용해 확장 이넘 타입을 만들 수 있다.

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
