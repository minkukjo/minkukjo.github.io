---
layout: post
title: Effective Java 05 - item 31 -
subtitle: ''
categories: language
tags: java
comments: false
---

## 한정적 와일드카드를 사용해 API 유연성을 높이라

한정적 와일드 카드란 다음과 같다.

```java
public void pushAll(Iterable<? extends E> src) {
    for (E e : src) {
        push(e);
    }
}
```

이는 E의 하위 타입이 Iterable이어야 한다는 의미이다.

이러한 와일드 카드 타입 한정을 사용하면 조금 더 Type Safe한 API를 만들어 클라이언트에게 제공할 수 있다.

이러한 와일드 카드 타입을 언제 써야하는지 기억하는데 `PECS`라는 공식이 있다.

`Producer-Extends, Consumer-Super`라는 의미이다.

즉 매개변수화 타입 T가 생산자라면 `<? extends T>`를, 소비자라면 `<? super T>`를 사용하라는 의미이다.

pushAll의 src 매개변수는 Stack이 사용할 E 인스턴스를 생성하므로 src의 적절한 타입은 생산자이다.

반대로 소비자는 `Comparable<? super E>`가 존재한다.

또한 `?`를 사용하면 와일드카드 타입이 존재한다.

아래의 두 메소드 중 어느것이 더 유용할까?

```java
public static <E> void swap(List<E> list, int i, int j);
public static void swap(List<?> list, int i, int j);
```

public API라면 두 번재가 더 낫다.

어떤 리스트든 이 메서드에 넘기면 명시한 인덱스 원소를 교환해줄 것이기 때문이다.

그러나 이 코드를 실제로 문제가 발생한다.

왜냐하면 `List<?>`에는 null 외에는 어떤 값도 넣을 수 없기 때문이다.

이를 회피하기 위해 `private 도우미 메서드`를 사용할 수 있는데 다음과 같다.

```java
public static void swap(List<?> list, int i, int j) {
    swapHelper(list,i,j);
}

public static <E> void swapHelper(List<E> list, int i, int j) {
    list.set(i, list.set(), list.get(i));
}
```

도우미 메서드를 제네릭 메서드로 만들어서 이를 회피해볼 수 있다.

조금 복잡하더라도 와일드 카드 타입을 적용하면 API가 훨씬 유연해진다.

`PECS`를 꼭 기억하자.

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
