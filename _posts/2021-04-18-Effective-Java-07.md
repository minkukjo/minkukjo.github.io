---
layout: post
title: Effective Java 02 - item 05 -
subtitle: ''
categories: language
tags: java
comments: false
---

## 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라

하나의 클래스 안에서 다른 클래스를 사용할 때 종종 클래스 내에서 정적 변수로 선언하여 사용하는 것을 심심치않게 볼 수 있다.

이는 유연하지 못하고 테스트를 어렵게 만든다.

고로, 사용하려는 클래스를 의존성 주입을 통해 사용하게 하는 것이 바람직하다.

아래와 같다.

```java
public class Harry {
    private final Laptop laptop;

    public Harry(Laptop laptop) {
        this.laptop = Objects.requireNonNull(laptop);
    }
}
```

이러한 생성자를 통한 의존성 주입은 `Spring Framework`에서도 적극 권장하고 있는 방식이기도 하다.

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
