---
layout: post
title: Effective Java 05 - item 32 -
subtitle: ''
categories: language
tags: java
comments: false
---

## 제네릭과 가변인수를 함께 쓸 때는 신중하라

다음과 같은 예시 코드를 보자.

```java
public static void dangerous(List<String> ...stringLists) {
    List<Integer> intLists = List.of(42);
    Object[] objects = stringLists;
    object[0] = intList; // 오염 발생
    String s = stringLists[0].get[0] // ClassCastException
}
```

제네릭 varargs 배열 매개변수에 값을 저장하는 것은 안전하지 않다.

자바 7부터는 `@SafeVarargs` 애너테이션이 추가되어 제네릭 가변인수 메서드 작성자가 클라이언트 측에서 발생하는 경고를 숨길 수 있게 되었다.

즉, `@SafeVarargs`는 개발자가 해당 메서드가 안전하니 믿고 써도 좋다는 증명을 위한 장치이다.

그러나 가변인수와 제네릭은 궁합이 좋지 않다.

가변인수 기능은 배열을 토출하여 추상화가 완벽하지 못하고 제네릭과 배열 타입 규칙이 서로 다르기 때문에 혼용해서 사용하지 말자.

그러나 반드시 필요한 경우라면 메서드가 타입 안전한지 확인한 다음 `@SafeVarargs` 애너테이션을 붙여 클라이언트에게 경고 메시지가 발생하지 않도록 하자.

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
