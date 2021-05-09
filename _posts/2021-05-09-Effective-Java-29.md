---
layout: post
title: Effective Java 05 - item 27 -
subtitle: ''
categories: language
tags: java
comments: false
---

## 비검사 경고를 제거하라

제네릭은 컴파일단에서 잘못된 타입을 사용하려고 할 경우 알려준다.

여기서 의미하는 비검사 경고란 컴파일러가 자바 코드를 컴파일하고 나서 잘못된 것들에 대해 경고하는 옵션을 의미한다.

`javac 명령 인수에 -Xlint:uncheck`를 추가하면 해당 에러를 볼 수 있다.

실제로는 IDE 단에서 이러한 에러를 잡아주므로 가급적 IDE가 알려주는 에러는 다 제거하도록 하자.

비검사 경고는 자칫 잘못하면 런타임 에러를 유발할 수 있는 위험성을 내포하고 있다.

만약 경고를 없앨 수 없다면 `SuppressWarnings("unchecked)` 애너테이션으로 경고를 숨겨놓자.

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
