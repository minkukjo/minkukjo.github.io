---
layout: post
title: Effective Java 02 - item 06 -
subtitle: ''
categories: language
tags: java
comments: false
---

## 불필요한 객체 생성을 피하라

똑같은 기능의 객체를 매번 생성하기보다는 객체 하나를 재사용하는 편이 더 낫다.

```java
String s = new String("harry");
```

위 코드는 매우 나쁜 코드이다. 실행될 때 마다 String 인스턴스를 새롭게 만든다.

생성자에 넘어가는 `harry`라는 문자열이 생성자로 만드려는 `String`과 일치한다.

개선해보자.

```java
String s = "harry";
```

만약 정규식 검증과 같이 비싼 작업이라면 매번 실행될 때 마다 호출하기 보다는, 정적 멤버로 만들어서 캐싱해두고 재사용을 하는 편이 더 낫다.

이러한 패턴의 안좋은 예는 아래와 같다.

```java
private static long sum() {
    Long sum = 0L;
    for(long i=0; i<=Integer.MAX_VALUE; i++)
        sum += i;
    return sum;
}
```

문제점을 찾았는가?

저자는 이 코드를 동작시키는데 6.5초가 걸렸고, sum의 박싱타입인 Long을 long으로 바꾸기만 했는데 0.59초로 빨라졌다고 말하고 있다.

즉, Primitive 타입을 박싱 타입으로 선언하면 자동으로 언박싱을 하게 되는데, 이 과정에서 꽤 많은 연산이 생긴다는 것을 알 수 있다.

그렇다고 해서 이번 주제를 `객체 생성은 비싸니 피해야한다`로 오해해서는 안된다.

요즘같은 메모리가 싸고 널널한데다가 나날이 올라가는 가비지 콜렉터의 성능을 가진 JVM에서 이정도 작은 객체 생성과 회수는 크게 부담되는 일은 아니다.

그렇다고 해서 별로 비싸지도 않은 객체의 `Cusmtom Pool`을 만들어 관리하지는 말자.

DB 연결이야 워낙 비용이 비싸니 재사용하는 편이 낫다지만, 작은 객체까지 그럴 필요는 없다.

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
