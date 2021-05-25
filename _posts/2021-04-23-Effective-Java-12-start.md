---
layout: post
title: Effective Java 03 모든 객체의 공통 메서드
subtitle: ''
categories: language
tags: java
comments: false
---

## 모든 객체의 공통 메서드

`Object`는 자바에서 모든 오브젝트의 최상위 클래스이며 **상속**을 통해서 사용하도록 설계되었다.

`Object` 클래스에서 final이 아닌 메서드들 equals, hashCode, toString, clone, finalize 모두 오버라이딩을 염두에 두고 설계된 것이기 때문이다.

이번 장에서는 final아 아닌 메서드들을 언제, 어떻게 재정의를 해야하는지에 대해서 살펴보자.

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
