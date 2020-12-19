---
layout: post
title: 플러시
subtitle: ''
categories: framework
tags: jpa
comments: false
---

## 플러시

영속성 컨텍스트의 변경 내역을 DB에 반영하는 것이 플러시이다.

## 플러시 발생하면 무슨 일이 벌어지는가

DB **transaction.commit**()이 발생하면 자동으로 플러시가 발생한다.

- 변경 감지 (Dirty Checking)

- 수정된 엔티티 쓰기 지연 SQL 저장소에 등록

- 쓰기 지연 SQL 저장소의 쿼리를 DB에 전송 (등록, 수정, 삭제 쿼리)

플러시가 발생한다고 해서 DB 트랜잭션이 커밋되는 것은 아니다.

플러시가 되고 나서, 커밋이 된다.

## 영속성 컨텍스트를 플러시하는 방법

- em.flusH()라고 직접 호출 가능

- 트랜잭션 커밋 - 플러시 자동 호출

- JPQL 쿼리 호출 - 플러시 자동 호출

플러시가 호출되면 1차 캐시가 다 지워질까? -> 아님

## JPQL 쿼리 실행시 플러시가 자동으로 호출되는 이유

```java
em.persist(memberA);
em.persist(memberB);
em.persist(memberC);

query = em.createQuery("select m from Member m", Member.clss);
List<Member> members = query.getResultList();
```

persist에서 쿼리가 발생하지 않는다는 사실을 우리는 이전 시간에 배웠다. (쓰기 지연 SQL에 저장)

그러면 전체 멤버들을 조회하는 JPQL 쿼리가 실행되면 멤버들을 조회해올 수 있을까?

불가능하다.

그래서 JPQL은 실행될 때 쓰기 지연 저장소에 있는 쿼리들을 flush 시킨 후 쿼리를 발생시킨다.

## 플러시 모드 옵션

```java
em.setFlushMode(FlushModeType.COMMIT)
```

개발하면서 쓸 일은 없지만 알아만 두자.

기본값은 FlushModeType은 AUTO이다.

커밋이나 쿼리를 실행할 때 플러시가 된다.

COMMIT은 커밋할 때만 플러시가 된다. (쿼리는 X)

## 플러시는

- 영속성 컨텍스트를 비우지 않는다.

- 영속성 컨텍스트의 변경내용을 DB에 동기화한다.

- 트랜잭션이라는 작업 단위가 중요 -> 커밋 직전에만 동기화하면 된다.

## Reference

인프런 김영한님의 자바 ORM 표준 JPA 프로그래밍 - 기본편
