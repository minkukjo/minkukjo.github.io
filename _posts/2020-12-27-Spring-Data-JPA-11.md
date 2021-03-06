---
layout: post
title: 양방향 연관관계와 연관관계의 주인 - 기본
subtitle: ''
categories: framework
tags: jpa
comments: false
---

## 양방향 연관관계

먼저 단방향 연관관계를 생각해보자.

객체지향 세계에서 A라는 객체가 B라는 객체의 참조를 갖고 있으면 B 객체는 자신을 참조하는 객체가 누구인지 알 수 없었다.

그러나 테이블에서는 FK를 통해 나를 참조하고 있는 주체의 테이블이 누구인지 알 수 있다.

객체지향에서도 테이블에서와 마찬가지로 나를 참조하는 객체가 누구인지 알기 위해서는 B 객체에 A 객체의 정보를 갖고 있으면 된다.

만약 **한 팀에는 여러 멤버가 속할 수 있으며 멤버는 팀에 소속되거나 소속되지 않을 수 있다**라는 조건이 있다고 해보자.

위 요구사항은 양방향 연관관계를 갖는다.

객체지향에서는 멤버 클래스에 팀 객체를 갖고 있으면 되고, 팀 객체에서는 멤버의 **리스트**를 갖고 있으면 된다.

이를 다시 테이블 관계로 표현해보면, 멤버 테이블에는 팀 ID가 외래키로 존재하며, 팀은 ID(PK)가 존재하는 구조가 된다.

이를 코드로 구현해보면 다음과 같은 형태가 된다.

```java
@Entity
public class Team {
    ...

    @OneToMany(mappedBy = "team")
    private List<Member> members = new ArrayList<>();
}
```

```java
@Entity
public class Member {
    ...

    @ManyToOne
    @JoinColumn(name = "TEAM_ID")
    private Team team;
}
```

그러면 양방향 매핑이 좋은가?

객체는 가급적 양방향보단 단방향이 좋다.

## 연관관계의 주인과 mappedBy

위에서 mappedBy란 **내 반대편에는 team으로 매핑이 되어있어**라는 것을 알려준다.

이 mappedBy가 JPA의 멘탈붕괴급 난이도를 만들며 이해하기가 난해하다.

이를 이해하기 위해서는 우선 양방향 영관관계일 때 객체의 연관관계와 테이블의 연관관계를 이해해야한다.

### 객체 연관관계는 2개

멤버가 팀으로 가는 연관관계 1개

팀이 멤버로 가는 연관관계 1개

단방향의 연관관계가 2개 있는 것이다.

### 테이블의 연관관계는 1개

멤버의 외래키 값 하나로 양방향 연관관계를 표현할 수 있다.

그래서 테이블에서는 사실 방향이라는게 의미가 없다.

## 객체의 양방향 관계

그래서 사실 객체는 양방향 관계라는 것이 존재하지 않으며, 서로 다른 단방향 관계 2개를 만든 것이다.

## 둘 중 하나에는 외래키를 관리해야한다

여기서 하나의 딜레마가 생긴다.

객체는 두 방향으로 만들었다.

그럼 테이블 입장에서는 Member의 Team이 업데이트 됐을 때 외래키 값을 업데이트 해야하는지,

Team의 Member 값이 업데이트 됐을 때 외래키 값을 업데이트 해야하는지 딜레마가 발생한다.

이 패러다임의 문제를 조금 풀어보면 다음과 같다.

```
해리라는 Member가 Team에 들어가려고 한다.

그럼 Member 클래스의 Team의 값을 바꿔주는게 맞을까,

아니면 Team 클래스의 Member 리스트에 해리를 추가해주는게 맞을까?
```

이러한 문제가 발생하는 이유는 결국 **단방향 연관관계가 2개**이기 때문에 발생하는 문제이다.

DB 입장에서는 외래키를 어떻게 업데이트를 해줘야할지 난감해진다.

그래서 JPA에서는 외래키를 관리하는 관리 주체를 정했고 이것이 바로 **연관 관계의 주인**이다.

## 연관관계 주인 (Owner)

연관 관계의 주인은 양방향 관계에서만 존재한다.

이 주인은 외래키를 관리(등록, 수정)할 수 있다.

**주인이 아닌쪽은 읽기만 가능하다**.

연관관계 주인은 mappedBy 속성을 사용하지 않는다.

객체의 두 관계중 하나를 연관관계의 주인으로 지정한다.

mappedBy는 어떻게 보면 readOnly와도 같은 의미인데, 내가 어떤 것에 의해 매핑되었다라는 의미이다.

그리고 주인이 아니면 mappedBy 속성으로 주인을 지정해준다. (내 주인님이 누구인지를 mappedBy 속성을 통해 명시해준다.)

## 누구를 주인으로 설정하는게 맞을까?

정답은 **외래키가 있는 곳이 주인이 되어야한다**.

위 예제에서는 Member.team이 연관관계의 주인이다.

Member 테이블이 외래키를 가지므로, 외래키를 가지는 테이블과 매핑되는 **Entity**가 연관관계의 주인이 되어야한다.

## Reference

인프런 김영한님의 자바 ORM 표준 JPA 프로그래밍 - 기본편
