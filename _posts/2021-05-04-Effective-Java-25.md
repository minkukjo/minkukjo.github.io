---
layout: post
title: Effective Java 04 - item 23 -
subtitle: ''
categories: language
tags: java
comments: false
---

## 태그 달린 클래스보다는 클래스 계층구조를 활용하자

태그 달린 클래스란 클래스 내부에서 enum을 사용하여 분류를 하는 클래스를 의미한다.

이 태그 클래스는 클래스 하나 안에 여러 구현이 혼합되어있어 가독성을 해치게 된다.

한 마디로, 태그 달린 클래스는 장황하며 오류를 내기 쉽고 비효율적이니 쓰지 말자.

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
