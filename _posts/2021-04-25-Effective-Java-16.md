---
layout: post
title: Kafka
subtitle: ''
categories: language
tags: java
comments: false
---

## Comparable을 구현할지 고려하라

이번 장의 마지막에서는 `Comparable` 인터페이스의 유일한 메서드인 `compareTo`에 대해서 알아보자.

눈치 챘을지 모르겠지만, `compareTo`는 유일하게 이번 장에서 `Object`의 메서드가 아닌 메서드이다.

성격은 두 가지만 빼면 `Object`의 `equals`와 같다.

`compareTo`와 `equals`와 다른 점 첫 번째는, 동치성 비교에 더해 **순서**까지 비교할 수 있다는 점이며, 제네릭하다는 것이다.

즉, `Comparable` 구현했다는 것은 해당 클래스의 인스턴스들에게 자연적인 순서가 있음을 의미한다.

사실상 자바 플랫폼 라이브러리의 모든 값 클래스와 열거 타입은 `Comparable`를 구현하고 있다.

만약에 순서가 명확한 값 클래스를 작성한다면 반드시 `Comparable` 인터페이스를 구현하도록 하자.

`compareTo`는 이전 `hashCode`와 마찬가지로 규약을 지켜야한다.

첫 번째 규약은 두 객체의 참조의 순서를 바꿔 비교해도 항상 예상한 결과가 나와야한다.

두 번째 규약은 첫 번째가 두 번째보다 크고 두 번째가 세 번째보다 크다면, 아래의 수식이다.

```
a > b 이고,
b > c 라면,
a > c 이다.
# 이산수학인가..?
```

마지막 규약은 크기가 같은 객체들 끼리는 어떤 객체와 비교하더라도 항상 같아야한다는 것이다.

위 세 규약은 반사성, 대칭성, 추이성을 충족해야한다는 것을 의미하기도 한다.

`compareTo`의 마지막 규약은 필수는 아니지만, 지키게 되면 이는 `equals` 메서드와 같은 결과를 얻을 수 있게 된다.

물론 정렬된 컬렉션에서 `compareTo`의 결과가 `equals`의 결과와 다르더라도, 문제는 없다.

참고로 정렬된 컬렉션들은 동치성 비교에 `equals`가 아닌, `compareTo`를 사용한다.

그러니 이는 큰 문제는 아니지만 가급적 지켜주는 것이 좋다.

자바 8에서는 `Comparator` 인터페이스가 일련의 비교자 생성 메서드와 팀을 꾸려 메서드 연쇄 방식으로 비교자를 생성할 수 있게 지원한다.

이 방법은 간결하고 매력적이지만, 약간의 성능 저하가 있다고 한다.

모던 자바를 사용하는 프로젝트라면 이 방법을 적극 사용하는 편이 좋다.

```java
static Comparator<Object> hashCodeOrder = Comparator.comparingInt(o -> o.hashCode());
```

순서를 고려해야하는 값 클래스를 만든다면 `Comparable` 인터페이스를 구현하여 그 인스턴스를 쉽게 정렬, 검색, 비교할 수 있게 하는 컬렉션과 어우러지도록 해야한다.

그리고 Primitive 타입의 비교가 필요하다면 박싱 클래스가 제공하는 정적 `compare` 메서드나 `Comparator` 인터페이스가 제공하는 비교자 생성 메서드를 사용하도록 하자.

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
