---
layout: post
title: Effective Java 05 - item 33 -
subtitle: ''
categories: language
tags: java
comments: false
---

## 타입 안전 이종 컨테이너를 고려하라

제네릭은 `Set<E>`, `Map<K,V>`등의 컬렉션이나 `ThreadLocal<T>`와 같은 단일 원소 컨테이너에서도 흔히 쓰인다.

데이터베이스의 경우 행(row)은 임의 개수 열(column)을 가질 수 있는데, 모두 열을 타입 안전하게 사용할 수 있으면 꽤 좋을 것이다.

컨테이너 대신 키를 매개변수화한 다음, 컨테이너에 값을 넣거나 뺄 때 매개변수화한 키를 함께 제공하면 된다.

이러면 제네릭 타입 시스템이 값의 타입이 키와 같음을 보장해줄 것이다. 이러한 설계 방식을 이종 컨테이너 패턴 `type safe heterogeneous container pattern`이라고 부른다.

아래 코드를 봐보자. 키가 매개변수화 되었다는 점만 빼면 일반 맵처럼 보일 것이다.

```java
public class Favorites {
    public <T> void putFavorite(Class<T> type, T intsnace);
    public <T> T getFavorite(Class<T> type);
}
```

위 Favorites 클래스를 활용하여 String, Integer, Class 인스턴스를 저장, 검색 해보자.

```java
public static void main(String[] args) {
    Favorites f = new Favorites();

    f.putFavorite(String.class, "Java");
    f.putFavorite(Integer.class, 12312412);
    f.putFavorite(Class.class, Favorites.class);

    String favoriteString = f.getFavorite(String.class);
    int favoriteInteger = f.getFavorite(Integer.class);
    Class<?> favoriteClass = f.getFavorite(Class.class);

    // 출력
}
```

Favorites는 `Type Safe`하다.

맵과 달리 여러 타입의 원소를 담을 수 있다.

따라서 Favorites는 타입 안전 이종 컨테이너라 부를만 하다.

컬렉션 API로 대표되는 일반적인 제네릭 형태에서 한 컨테이너가 다룰 수 있는 타입 매개변수의 수가 고정되어있다.

컨테이너 자체가 아닌 키를 매개변수로 두면 이러한 제약이 없는 타입 안정 이종 컨테이너를 만들 수 있다.

타입 안전 이종 컨테이너에서는 Class를 키로 쓰며, 이런 식으로 쓰이는 Class 객체를 타입 토큰이라고 부른다.

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
