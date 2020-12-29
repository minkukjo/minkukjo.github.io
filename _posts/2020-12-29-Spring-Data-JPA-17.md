---
layout: post
title: 즉시 로딩과 지연 로딩
subtitle: ''
categories: framework
tags: jpa
comments: false
---

## 지연 로딩 LAZY를 사용해 프록시로 조회

```java
class Member {
    ...

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "TEAM_ID")
    private Team team;

    ...
...
}
```

LAZY를 주면 JPA가 Team 프록시 객체를 넘겨주고 실제 DB에 쿼리를 해오지 않는다.

이것이 바로 JPA의 강력한 기능인 **Lazy Loading**이다!

그러면 언제 실제 쿼리가 날아가느냐?

바로 Team에 해당하는 어떤 값을 가져오려는 경우에 비로소 쿼리를 날린다.

근데 이러면 결국 쿼리를 두 번 날리게 되는 것이니 성능상 손해가 있을 수 있다.

만약 비즈니스적으로 Member와 Team이 쫀쫀하게 붙어간다면 어떨까?

## 즉시 로딩

```java
class Member {
    ...

    @ManyToOne(fetch = FetchType.EAGER)
    @JoinColumn(name = "TEAM_ID")
    private Team team;

    ...
...
}
```

Member를 조회할 때 한방 쿼리를 통해 Team도 같이 쿼리해온다.

그래서 이름이 **EAGER LOADING**인 것이다!

여기서는 Team의 프록시 객체를 사용하지않고 **진짜** Entity를 가져오게 된다.

## 프록시와 즉시로딩에서 주의할 부분

김영한님의 말씀을 들으면 실무에서는 **가급적 지연 로딩만 사용하는 것이 좋다**고 한다.

한 방에 조인쿼리 날리면 좋은데 왜 일까?

1. 즉시 로딩을 적용하면 **전혀 예상하지 못한 SQL**이 발생할 수 있다.

2. 즉시 로딩은 **JPQL에서 N+1 문제**를 발생시킨다. (가장 일반적인 해결법은 fetch 조인)

3. @ManyToOne, @OneToOne은 기본이 즉시 로딩이므로 LAZY로 설정해준다.
   - OneToMany와 @ManyToMany는 기본이 지연 로딩이다.

## 결론 : 무조건 싹 다 지연 로딩 써라

**실무에서 즉시 로딩 쓰지 마라**!

## Reference

인프런 김영한님의 자바 ORM 표준 JPA 프로그래밍 - 기본편
