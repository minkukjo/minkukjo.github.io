---
layout: post
title: Fetch Join - 기본
subtitle: ''
categories: framework
tags: jpa
comments: false
---

## Fetch Join

이번 시간에는 JPQL에서 매우 매우 매우 중요한 fetch join에 대해서 알아보자.

이름을 들어보면 알겠지만 SQL의 조인 종류가 아니다.

**오직 JPQL**에서 **성능 최적화**를 위해 제공하는 기능이다.

연관된 엔티티나 컬렉션을 SQL 한 번에 함께 조회하는 기능이라고 보면 된다.

쿼리가 2번 날아갈 것 같은 상황에 한 번에 조회하는 기능이다.

## Entity Fetch Join

회원을 조회하면서 팀도 함께 조회하고 싶다고 가정하자.

SQL에서는 회원 뿐만 아니라 팀도 같이 조회를 한다.

이를 JPQL로 표현하면 다음과 같다.

```sql
select m from Member m join fetch m.team
```

얼핏 보면 SQL과 유사해보이는데 차이점이 있다.

우선 첫 번째는, join 뒤에 fetch가 붙는다.

그리고 프로젝션에 m(Member)만 존재한다. (이 부분이 매우 중요)

위 JQPL은 아래의 SQL로 변역된다.

```sql
SELECT M.*, T.* FROM MEMBER M
INNER JOIN TEAM T ON M.TEAM_ID=T.ID
```

즉, Fetch Join의 목적은 관계 있는 Entity를 한 방에 가져오는데 그 목적이 있다.

즉, Fetch Join은 지연로딩을 하지 않고 즉시 로딩(EAGER)로 가져오는 쿼리이다.

가령 N명의 멤버는 1개의 팀을 가질 수 있다는 다대일 관계를 생각해보자.

여기서 N명의 멤버를 가져오기위한 쿼리가 발생할 것이고,

이후 각 멤버는 팀을 프록시 객체로 받아온 뒤 프록시 객체의 값에 접근하려고 할 때 실제 쿼리를 날려서 팀의 정보를 가져오게 된다.

문제는 이 때, N명의 멤버가 모두 각기 다른 팀을 가진다고 가정하면 1명의 멤버마다 그 멤버가 소속된 팀의 쿼리가 나아갈 것이고 (1차캐시가 안될거니까) 이것이 우리가 흔히 말하는 **N+1** 문제가 발생하게 되는 원인이다.

사실 이러한 N+1 문제는 지연로딩이든 즉시로딩이든 발생하는 문제인데, 이를 해결하는 방법 중 하나가 바로 JPQL의 **Fetch Join**이다.

컬렉션 페치 조인의 문제가 하나 있는데, 이게 Join이다 보니, 1:N 조인은 반드시 데이터가 **뻥튀기**될 수 있다.

<img width="445" alt="스크린샷 2021-01-04 오후 5 30 06" src="https://user-images.githubusercontent.com/43809168/103515686-86582180-4eb2-11eb-819e-5d576e83c9af.png">

팀 A 입장에서는 하나이지만, 멤버가 2명이어서 2 ROW가 된다.

## Fetch Join과 DISTINCT

위와같이 중복 ROW가 생기는게 싫을 수 있다.

SQL의 DISTINCT는 중복 결과를 제거하는 명령이다.

이 명령만으로는 중복을 제거하기가 어려워서 JPQL에서 DISTINCT를 제공한다.

JPQL의 DISTINCT 역할은 두 가지 역할을 수행한다.

1. SQL에 DISTINCT를 추가한다.

2. 애플리케이션의 Entity 중복을 제거한다.

```sql
select distinct t From Team t join fetch t.members;
```

위 결과를 수행하면 **distinct** 후에 애플리케이션으로 데이터가 올라올 때 중복 Entity를 JPA가 제거해준다.

## Fetch Join과 일반 Join의 차이?

일반 조인 실행 시 연관된 **Entity**를 함께 조회하지 않는다.

실제 수행 쿼리를 보면 조인 쿼리가 날아가기는 하는데 프로젝션에서 연관 **Entity** 정보를 조회하지는 않는다.

물론 그렇다고 해서 데이터가 프록시인 것은 아니지만 해당 데이터에 접근하려고 하면 로딩 시점에 쿼리가 날아간다.

## N+1

대부분의 N+1 문제는 JPQL Fectch Join으로 해결할 수 있다.

## Reference

인프런 김영한님의 자바 ORM 표준 JPA 프로그래밍 - 기본편
