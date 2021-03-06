---
layout: post
title: Effective Java 04 - item 19 -
subtitle: ''
categories: language
tags: java
comments: false
---

## 상속을 고려해 설계하고 문서화하라. 그러지 않았다면 상속을 금지하라.

상속용 클래스에서 재정의할 수 있는 메서드들을 내부적으로 어떻게 이용할 수 있는지에 대한 문서를 **반드시** 남겨야한다.

아이러니하게도 클래스를 안전하게 상속하게 하기 위해선 굳이 설명할 필요도 없었던 내부 구현 방식을 자세하게 설명해야만한다.

클래스의 내부 동작 과정 중간에 끼어들 수 있는 hook을 잘 선별하여 protected 메서드 형태로 제공하여 공개하는 것도 좋은 방법이다.

그리고 상속용 클래스의 생성자는 직접적으로든 간접적으로든 재정의 가능 메서드를 호출해서는 안된다.

이를 어기면 의도치않은 동작이 실행될 수 있다.

상위 클래스의 생성자가 하위 클래스의 생성자보다 먼저 실행되므로, 하위 클래스에서 재정의한 메서드가 하위 클래스의 생성자보다 더 먼저 호출되어버린다.

그런데 거기서 재정의 메서드가 하위 클래스의 생성자에서 초기화되는 값에 의존되어버린다면 이는 동작이 이상하게 될 것이다.

클래스를 상속용으로 설계하는 것에는 엄청난 노력과 비용이 들며, 클래스 자체에 제약도 상당하다.

고로 이러한 문제를 피하기 위해 상속용이 아닌 클래스는 상속을 금지시켜버리는 편이 더 낫다.

코틀린의 모든 기본 클래스는 `final` 클래스이다. 

이는 기존 자바의 클래스 상속을 피하기 위함으로 보인다.

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
