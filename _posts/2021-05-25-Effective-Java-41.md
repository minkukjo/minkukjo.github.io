---
layout: post
title: Effective Java 06 - item 39 -
subtitle: ''
categories: language
tags: java
comments: false
---

## 명명 패턴보다 애너테이션을 사용하라

명명 패턴이란, 메서드 이름을 반드시 `test`로 시작해야한다는 (junit 3에서는 실제로 이랬다고 한다) 메소드명 규칙을 의미한다.

문제는 이러한 명명 패턴은 오타를 내서는 안되며, 실수로 이름을 `tset어쩌고저쩌고`로 지으면 JUnit 3가 그냥 지나가버리기 때문에 개발자는 이 테스트가 통과했다고 오해를 할 수 있다.

이러한 방법은 JUnit 4에서 애너테이션으로 변경되었고 `@Test` 애너테이션이 붙은 메소드들만 테스트의 대상이 되었다.

자바에서 애너테이션은 단순히 주석으로 취급한다.

왜냐하면 애너테이션은 기본 값이 컴파일 시점에 사라지기 때문이다.

그러나 애너테이션 Retention을 바꾸면 런타임에도 사라지지않고 남아있는 애너테이션을 정의할 수 있다.

이러한 런타임에도 존재하는 애너테이션을 자바 리플렉션 API를 사용해 코드를 조작하여 개발한 프레임워크가 바로 `Spring Framework`이다.

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
