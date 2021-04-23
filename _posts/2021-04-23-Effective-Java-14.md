---
layout: post
title: Effective Java 03 - item 12 -
subtitle: ''
categories: language
tags: java
comments: false
---

## toString을 항상 재정의하라

자바에서 프레임워크를 쓰다보면 종종 `toString()` 메소드로 인해 고통받는 일이 생긴다.

이번 시간에는 Object의 메서드 중 하나인 `toString()`에 대해서 알아보자.

Object 클래스의 기본 toString 메서드는 꽤나 불친절하다.

가령 `PhoneNumber` 클래스의 toString을 호출해보면 `Phonenumber@abbdd`와 같은 출력 결과를 확인해볼 수 있다.

toString()은 인간이 읽기 편한 형태가 되어야하므로, 우리는 이 toString()를 재정의해볼 것이다.

toString() 메서드는 우리가 직접 호출하지않더라도, `println`이나 `assert` 구문에 넘길 때, 혹은 디버거가 객체를 출력할 때 자동으로 호출되는 메소드이다.

즉, 우리가 명시적으로 호출하지않더라도 어딘가에서는 쓰일 수 있다는 이야기이다.

toString을 똑바로 정의했다면 아래와 같은 코드는 그 의미를 명확히 파악할 수 있게 될 것이다.

```java
System.out.println(phonNumber + "에 연결할 수 없습니다.");
```

즉, toString을 사용한다면 **가급적 해당 객체가 갖고 있는 모든 정보들을 노출시켜주는 편이 좋다.**

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
