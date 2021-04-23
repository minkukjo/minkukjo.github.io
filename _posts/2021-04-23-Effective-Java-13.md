---
layout: post
title: Effective Java 03 - item 11 -
subtitle: ''
categories: language
tags: java
comments: false
---

## equals를 재정의하려거든 hashCode도 재정의하라

**equals를 재정의한 클래스라면 모두 hashCode 또한 재정의해야 한다.**

그렇지않으면 해당 클래스의 인스턴스를 **HashMap**이나 **HashSet**같은 컬렉션의 원소로 사용할 때 문제를 일으킬 수 있다.

**hashCode**를 잘못 재정의헀을 때 크게 나타날 수 있는 문제는 **논리적으로 같은 객체는 같은 해시코드를 반환해야한다**
는 점이다.

이 역시도 `lombok` 라이브러리를 사용하거나 Kotlin의 `data` 클래스를 사용하면 손쉽게 `Equals`와 `HashCode` 메서드를 알아서 재정의해준다.

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
