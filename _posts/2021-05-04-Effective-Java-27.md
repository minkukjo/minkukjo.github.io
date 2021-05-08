---
layout: post
title: Effective Java 04 - item 25 -
subtitle: ''
categories: language
tags: java
comments: false
---

## 톱 레벨 클래스는 한 파일에 하나만 담으라

소스 파일 하나에 톱 레벨 클래스를 여러 개 선언해도 자바 컴파일러는 불평하지 않는다.

하지만 딱히 이득이 없기 때문에 가급적이면 톱레벨 클래스는 한 파일에 하나만 넣는 편이 좋다.

(코틀린은 .Kt 파일을 지원하면서 이러한 제약을 없앴다.)

소스 파일 하나에는 반드시 하나의 톱레벨 클래스(또는 톱 레벨 인터페이스)를 담는 것이 자바의 관례이다.

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
