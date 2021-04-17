---
layout: post
title: Effective Java 02 - item 02 -
subtitle: ''
categories: language
tags: java
comments: false
---

## 제 2장 - 객체 생성 파괴 -

객체를 만들어야할 때와 만들어야하지 않을 때를 구분해야한다.

올바른 객체 생성과 파괴에 대해서 공부해보자.

## 생성자에 매개변수가 많다면 빌더를 고려하라

매개변수가 많은 함수는 가급적 피하는게 좋다는 것을 이미 클린코드에서 배웠다.

생성자 역시 너무 많은 매개변수와 다양한 생성자를 클래스가 갖게 되면 개발자 입장에서 사용하기가 여간 껄끄럽다.

결국 매개변수가 많은 생성자는 **코드의 가독성을 떨어뜨린다**

이를 위해 자바빈즈(Java Beans) 패턴을 사용할 수 있는데 아래와 같다.

```java
Member member = new Member();
member.setId(1L);
member.setName("harry");
member.setAge(18);
member.setMoney(0L);
```

이러한 자바 빈즈 패턴은 심각한 단점을 갖고 있는데, 보다시피 **객체 하나를 만들기 위해 여러 메서드를 호출해야한다**는 점이다.

또한 객체가 완전히 생성되기 전까지는 일관성이 무너진 상태에 놓이게 된다.

자바빈즈 패턴이 유발하는 또 다른 문제는 클래스를 불변으로 만들 수 없으며 스레드 안정성을 얻으려면 프로그래머가 추가 작업을 해줘야만 한다는 점이다.

이를 위한 가장 완벽한 대안은 **빌더 패턴**을 사용해보는 것이다.

빌더 패턴으로 위 멤버를 생성해보면 다음과 같다.

```java
Member member = new Member.Builder()
                    .id(1L)
                    .name("harry")
                    .age(18)
                    .money(0L);
```

어떤가? 이전보다 훨씬 읽기 쉽고 단순하고 명확해졌다.

빌더 패턴은 자바 API에서도 심심치않게 등장하며 `Lombok`을 사용한다면 이 빌더를 자동으로 만들어준다는 장점이 있다.

생성자나 정적 팩터리가 처리해야할 매개변수가 많다면 처음부터 빌더 패턴을 고려해보는 것이 더 낫다.

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
