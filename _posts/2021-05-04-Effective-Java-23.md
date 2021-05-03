---
layout: post
title: Effective Java 04 - item 21 -
subtitle: ''
categories: language
tags: java
comments: false
---

## 인터페이스는 구현하는 쪽을 생각해 설계하라

자바 8에서는 핵심 컬렉션 인터페이스들에 다수의 디폴트 메서드가 추가되었는데 이는 모두 람다를 활용하기 위해서이다.

디폴트 메서드는 기존 구현체에 런타임 오류를 일으킬 수 있다.

때문에 기존 인터페이스에 새로운 디폴트 메서드를 추가하는 일은 가급적 피하는 편이 더 좋다.

디폴트 메서드를 통해 설계가 더욱 편리해졌지만, 여전히 세심한 주의를 기울여야한다.

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
