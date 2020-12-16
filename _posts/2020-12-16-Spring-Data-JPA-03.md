---
layout: post
title: 영속성 컨텍스트 2
subtitle: ''
categories: framework
tags: jpa
comments: false
---

## 엔터티 조회와 1차 캐시

엔터티 매니저에 객체를 영속화하면 영속성 컨텍스트 내부에선 무슨 일이 일어날까?

영속성 컨텍스트(EntityManager) 안에는 1차 캐시가 존재한다.

이 1차 캐시는 `Map`으로 존재한다.

PK가 **Key**이며 **Value**가 **Entity** 자체가 된다.

이렇게 되면 어떤 이점이 있을까?

당연히 1차 캐싱이되면 조회를 할 때 **DB**에서 가져오는 것이 아닌 캐시에서 가져오게 된다.

사실 이 **1차 캐시는 그렇게 큰 도움은 안된다.**

왜냐하면 이 영속성 컨텍스트는 트랜 잭션과 생명주기를 함께한다.

즉, 트랜잭션이 종료되면 영속성 컨텍스트도 종료되기 때문에 엄청난 성능적 이점을 얻기가 애매하다.

(물론 비즈니스가 엄청나게 복잡하면 쿼리가 좀 줄어들긴 함)

실제로 성능적 이점을 가져다 주는 것은 2차 캐시 (Redis)이다.

## 영속 엔티티의 동일성 보장

```java
Member a = em.find(Member.class, "member1");
Member b = em.find(Member.class, "member1");

System.out.println(a==b); // true
```

1차 캐시로 Repetable Read 등급의 트랜잭션 격리 수준을 데이터베이스가 아닌 애플리케이션에서 제공한다.

## 엔티티 등록 - 트랜잭션을 지원하는 쓰기 지연

```java
transaction.begin();

em.persist(memberA);
em.persist(memberB);
// 여기까지 INSERT SQL을 DB에 보내지 않는다.

// 커밋하는 순간 데이터베이스에 INSERT SQL을 보낸다.
transaction.commit(); // 트랜잭션 커밋
```

어떻게 이것이 가능한 것인가?

영속성 컨텍스트 내부에는 1차 캐시 외에도 **쓰기 지연 SQL 저장소**라는 재미난 공간이 있다.

이곳에는 persist 메소드 호출 시 Entity가 1차 캐시에 저장 하고, JPA가 Entity를 분석해서 **INSERT SQL를 쓰기 지연 SQL 저장소**에 저장한다.

**쓰기 지연 SQL 저장소**가 DB에 날아가는 시점은 `transaction.commit()` 메소드가 호출한 시점에 `flush` 되면서 `commit` 된다.

그러면 이 기능이 주는 이점은 무엇일까?

하이버네이트 옵션 중에 `hibernate.jdbc.batch_size`라는 설정이 있는데 이 사이즈에 설정된 값 만큼 쿼리를 `한 방에` 보내게 된다.

즉 네트워크 연결 한 번에 여러 개의 쿼리가 발생하고 커밋되기 때문에 성능상 이점이 있을 수 있다.

그러니 일종의 `버퍼링` 기능이 있다고 할 수 있겠다.

소프트웨어에서 A와 B 사이의 중간 계층을 뒀을 때 대표적인 장점 중 두 가지는 `버퍼링`과 `캐싱`인데 이것이 바로 JPA를 쓰는 이점인 것이다.

## 엔티티 수정 - 변경 감지 (Dirty Checking)

```java
// 영속 엔티티 조회
Member memberA = em.find(Member.class, "memberA");

// 영속 엔티티 데이터 수정
memberA.setUsername("hi");
memberA.setAge(10);

//em.update(member) 이런 코드가 있어야 하지 않을까?

transaction.commit(); // 트랜잭션 커밋
```

위 코드가 실행되면 엔터티의 값을 바꿨음에도 **update 쿼리**가 발생하는 것을 볼 수 있다.

이 마법같은 일이 어떻게 발생할까?

이 비밀은 결국 **영속성 컨텍스트** 안에 있다.

JPA는 DB 트랜잭션이 커밋되는 시점에 내부적으로 영속성 컨텍스트 안에서 `flush()`라는 함수가 호출된다.

그 후 엔티티와 스냅샷을 비교하는데, 이 때 1차 캐시를 참조한다.

이 1차 캐시 내부에는 **Key**인 `ID`와 **Value**인 `Entity` 외에 또 다른 컬럼인 `SnapShot`이라는 제 3의 컬럼이 존재한다.

이 스냅샷은 값을 읽어온 최초의 시점의 스냅샷을 1차 캐시에 떠둔다.

`flush()`가 호출되면 엔티티와 스냅샷을 비교해서 바뀐게 있으면 **Update SQL**을 생성하고 쓰기 저장 SQL에 저장하고, 이 Update 쿼리를 DB에 발생시키고 **커밋**하게 된다. (오오오 놀라워라...)

## 엔티티 삭제

엔티티 삭제는 별거 없고 commit 시점에 delete 쿼리가 발생한다.

## Reference

인프런 김영한님의 자바 ORM 표준 JPA 프로그래밍 - 기본편
