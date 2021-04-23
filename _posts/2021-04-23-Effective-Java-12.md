---
layout: post
title: Effective Java 03 - item 10 -
subtitle: ''
categories: language
tags: java
comments: false
---

## 모든 객체의 공통 메서드

`Object`는 자바에서 모든 오브젝트의 최상위 클래스이며 **상속**을 통해서 사용하도록 설계되었다.

`Object` 클래스에서 final이 아닌 메서드들 equals, hashCode, toString, clone, finalize 모두 오버라이딩을 염두에 두고 설계된 것이기 때문이다.

이번 장에서는 final아 아닌 메서드들을 언제, 어떻게 재정의를 해야하는지에 대해서 살펴보자.

## eqauls는 일반 규약을 지켜 재정의하라

결론부터 이야기하자면, equals 메소드는 가급적 재정의하지 않는 편이 좋다.

개인적인 의견이지만, `lombok` 라이브러리에서 `@EqualsAndHashCode`를 지원하는 마당에, 이 아이템이 어떤 의미를 갖는지에 대해서 의문이 들었다.

**equals** 메서드를 재정의하는데에 있어 동치관계를 구현해줘야한다.

1. **반사성**(reflexivity) : null이 아닌 모든 참조값에 x에 대해 x.equals(x)는 true다.

2. **대칭성**(symmetry) : null이 아닌 모든 참조 값 x,y에 대해 x.equals(y)가 true면 y.equals(x)도 true이다.

3. **추이성**(transitivity) : null이 아닌 모든 참조 값 x,y,z에 대해 x.equals(y)가 true면 y.equals(x)도 true이면 x.equals(z)도 true이다.

4. **일관성**(consistency) : null이 아닌 모든 참조 값 x,y에 대해, x.equals(y)를 반복해서 호출하면 항상 true를 반환하거나 항상 false를 반환한다.

5. **null-아님** : null이 아닌 모든 참조 값 x에 대해, x.equals(null)은 true이다.

이후에 나오는 대부분의 예제는 Equals 메서드를 재정의하면서 발생할 수 있는 LCP(리스코프 치환 원칙) 위배 예제와 상속 대신 컴포지션을 사용하라는 식의 **객체지향**관련 이야기가 주를 이룬다.

(`Point` 클래스와 이를 상속받은 클래스 간의 종속성 문제를 이야기한다)

`equals`를 재정의하면서 주의해야할 또 다른 점은 `hashCode` 메소드도 반드시 재정의를 해주어야한다는 점인데, 이는 다음 아이템에서 소개할 예정이다.



## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
