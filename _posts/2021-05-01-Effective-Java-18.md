---
layout: post
title: Effective Java 04 - item 16 -
subtitle: ''
categories: language
tags: java
comments: false
---

## public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라

public 접근제어자 클래스라면 패키지 바깥에서 접근자를 제공함으로써 클래스 내부 표현 방식을 언제든 바꿀 수 있는 유연성을 제공하는 편이 더 낫다.

그러나 package-private 클래스나 private 중첩 클래스라면 데이터 필드를 노출한다 해도 하등 문제가 없고, 오히려 데이터 필드를 노출하는 편이 더 낫다.

public 클래스는 절대 가변 필드를 직접 노출해서는 안된다. 불변 필드라면 덜 위험하지만, 여전히 노출하지 않는 편이 대다수 경우에서 더 낫다.

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
