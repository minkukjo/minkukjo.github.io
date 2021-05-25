---
layout: post
title: Effective Java 06 - item 37 -
subtitle: ''
categories: language
tags: java
comments: false
---

## ordinal 인덱싱 대신 EnumMap을 사용하라

배열에서 인덱스를 얻고자 한다면 ordinal를 종종 사용할 수 있다.

그런데 사실 이러한 ordinal 인덱싱은 좋지않다.

ordinal을 통한 배열 인덱싱은 사실상 내부적으로 많은 문제를 야기한다.

가령 해당 타입의 정수 값이 정말 올바른지를 판단할 수 없어서 런타임에서나 되어야 에러를 만날 수 있다.

이런 상황에서는 `EnumMap`을 사용해보자.

EnumMap은 쉽게 말해 Enum의 값을 Key로 갖는 맵이다.

Map의 키가 Enum이므로 그 값의 범위와 상수를 보장해주어 더욱 더 안전하다.

그리고 Enum.ordinal은 가급적 사용하지 말아야한다.

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
