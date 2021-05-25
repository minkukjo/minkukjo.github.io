---
layout: post
title: Effective Java 06 - item 36 -
subtitle: ''
categories: language
tags: java
comments: false
---

## 비트 필드 대신 EnumSet을 사용하라

열거 값들이 주로 집합으로 사용되는 경우 각 상수에 서로 다른 2의 거듭제곱 값을 할당한 정수 열거 패턴을 사용해왔다고 한다.

아래를 보라.

```java
public class Text {
    public static final int STYLE_BOLD = 1 << 0;
    public static final int STYLE_ITALIC = 1 << 1;
    public static final int STYLE_UNDERLINE = 1 << 2;
}
```

상당히 올드패션의 기법이라고 한다.

이러한 비트 필드의 단점은 정수 열거 상수의 단점을 그대로 지닌데다가 다음의 문제까지 포함하고 있다.

비트 필드 값이 그대로 노출될 경우 단순 정수 열거 상수를 출력할 때 보다 해석하기가 더 어렵고, 비트 필드 하나에 녹아있는 모든 원소를 순회하기도 까다롭다.

열거타입의 집합을 사용해야한다면 EnumSet을 사용하도록 하자.

```java
public class Text {
    public enum Style { BOLD, ITALIC, UNDERLINE };

    public void applyStyles(Set<Style> styles) {
        ....
    }
}

Text text = new Text();
text.applyStyles(EnumSet.of(Style.BOLD, Style.ITALIC));
```

훨씬 깔끔하고 보기도 좋다.

열거 타입의 집합을 사용해야한다면 EnumSet을 사용하도록 하자.

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
