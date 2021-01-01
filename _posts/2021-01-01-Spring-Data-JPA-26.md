---
layout: post
title: 경로 표현식
subtitle: ''
categories: framework
tags: jpa
comments: false
---

## 경로 표현식이란?

- 점을 찍어 객체 그래프를 탐색하는 것을 의미한다.

```sql
select m.username -> 상태 필드
  from Member m
    join m.team t -> 단일 값 연관 필드
    join m.orders o -> 컬렉션 값 연관 필드
where t.name = '팀A'
```

## 경로 표현식 특징

- 상태 필드는 경로 탐색의 끝이다. 더 이상 탐색할 수 없다.

```sql
# 예시
select m.username # 상태 필드에서는 m.username. 해서 더 탐색할 수 없기 때문
  from Member m
```

- 단일 값 연관 경로는 묵시적 내부 조인이 발생하며 탐색할 수 있다.

```sql
# 예시
select m.team # 여기서 탐색을 또 할 수 있다. m.team.name이 가능하다는 뜻
  from Member m
```

- 컬렉션 값 연관 경로 또한 묵시적 내부 조인이 발생하지만 더 이상 탐색이 불가능하다.

```sql
# 예시
select t.members # t.members.XXX로의 추가 탐색이 불가능한 이유는 컬렉션으로 반환되는 값들 중 어떤 값을 선택해서 탐색할 지를 JPA가 알 수 없기 떄문.
  from Team t
```

- 이를 위해서는 **FROM** 절에서의 명시적 조인을 통해 탐색이 가능하다.

## 경로 탐색을 사용한 묵시적 조인 사용 시 유의사항

- 항상 내부 조인이 상요된다는 것을 명시하자

- 컬렉션은 경로 탐색으 끝이다.

- 경로 탐색은 주로 SELECT, WHERE 절에서 사용하지만 묵시적 조인으로 인해 FROM (JOIN) 절에 영향을 준다.

## 김영한님의 실무 조언

- 가급적 묵시적 조인 대신 명시적 조인을 사용하자

- 조인은 SQL 튜닝에 중요한 포인트

- 묵시적 조인은 조인이 일어나는 상황을 한눈에 파악하기 어렵다

## Reference

인프런 김영한님의 자바 ORM 표준 JPA 프로그래밍 - 기본편
