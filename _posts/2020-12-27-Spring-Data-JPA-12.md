---
layout: post
title: 양방향 연관관계와 연관관계의 주인 - 주의점, 정리
subtitle: ''
categories: framework
tags: jpa
comments: false
---

## 양방향 매핑시 가장 많이 하는 실수

**연관관계의 주인에 값을 입력하지 않는 경우**가 빈번하다.

양방향 매핑시에는 연관관계 양쪽 다 값을 입력하는 것이 **맞다**.

객체지향에서 순수한 객체 관계를 고려한다면 연관 관계의 주인과 매핑되는 쪽 둘 다 값을 넣어주는게 맞다.

## 기억해두어야할 점

```java
class Member {
    ...
    public void chageTeam(Team team) {
        this.team = team;
        team.getMembers().add(this);
    }
}
```

양방향 연관관계를 셋팅할 때는 아예 둘 중 하나의 클래스에다가 연관관계를 셋팅해주는 편이 나을 수 있다.

## 주의할 점

양방향 연관관게를 할 때 주의해야할 점은 바로 **무한 루프**이다.

이를 다르게 이야기하면 **순환 참조**라고도 하는데,

양방향 관계에서는 서로가 계속해서 참조를 해서 **StackOverFlow**가 발생할 수도 있다.

예를 들어, toString(), lombok, JSON 라이브러리 등이 이에 해당한다.

## 양방향 매핑 정리

**단방향 매핑만으로도** 사실 이미 연관관계 매핑은 완료된 것이나 다름없다.

실무에서 테이블을 설계할 때 단방향 매핑만으로도 이미 연관관계 매핑은 완료된다.

양방향 매핑은 반대 방향으로 조회(객체 그래프 탐색) 기능이 추가된 것 뿐이다.

JPQL에서는 역방향으로 탐색할 일이 많아서 양방향 매핑을 하는 경우가 종종 있다.

근데 단방향 매핑을 잘해두면 양방향은 필요할 때 추가가 가능하다. (테이블에 영향을 주지 않는다.)

## 연관관계의 주인을 정하는 기준

**연관관계의 주인은 외래 키의 위치를 기준으로 정해야한다**

## Reference

인프런 김영한님의 자바 ORM 표준 JPA 프로그래밍 - 기본편