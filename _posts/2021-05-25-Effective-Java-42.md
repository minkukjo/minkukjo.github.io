---
layout: post
title: Effective Java 06 - item 40 -
subtitle: ''
categories: language
tags: java
comments: false
---

## @Override 애너테이션을 일관되게 사용하라

상위 클래스에서 사용하는 메서드를 재정의하는 경우라면 반드시 `@Override` 애너테이션을 붙이는 편이 좋다.

추상 클래스의 추상메서드의 경우 굳이 `@Override`를 붙이지 않아도 괜찮지만, 가급적이면 일관성을 위해 붙이는 것도 나쁘지않다.

재정의할 모든 메서드에 `@Override`를 붙임으로써 실수했을 때 컴파일러가 알려줄 수 있도록 하자.

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
