---
layout: post
title: Effective Java 05 - item 30 -
subtitle: ''
categories: language
tags: java
comments: false
---

## 이왕이면 제네릭 메서드로 만들라

사실 제네릭 메서드는 꽤 유용하다.

제네릭 메서드를 잘 만들어두면 무궁무진하게 재활용할 수 있는 메서드를 만들 수 있다.

또한 이 제네릭 메서드 중 재귀적 타입 한정(recursive type bound)라는 개념이 존재하는데,

Comparable 인터페이스에서 아래처럼 쓰인다.

```java
public static <E extends Comparable<E>> E max(Collection<E> c);
```

빈번하게 사용하는 개념은 아니지만 자기 자신이 들어간 표현식을 사용하여 타입 매개변수의 허용 범위를 재한할 수 있다.

`<E extends Comparable<E>>`는 모든 타입 E는 자기 자신과 비교할 수 있다는 의미를 내포하고 있다.

제네릭 메서드는 클라이언트 입장에서 매개변수와 반환값을 명시적으로 형변환해야하는 메서드보다 더 안전하고 사용하기편하다.

타입도 그렇고 메서드도 그렇고 가급적 형변환 없이 사용하는 것이 가장 좋으며 그러기 위해 많은 경우에서 제네릭을 사용해야한다.

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
