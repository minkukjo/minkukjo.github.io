---
layout: post
title: 자바 ORM 표준 JPA 프로그래밍 - SQL의 문제점과 JPA 소개
subtitle: ''
categories: framework
tags: jpa
comments: false
---

## JPA의 등장과 SQL 중심 개발의 문제점

```java
public class Member {
    private String memberId;
    private String name;
}
```

```sql
INSERT INTO MEMBER ( MEMBER_ID, NAME ) VALUES
SELECT MEMBER_ID, NAME FROM MEMBER M
UPDATE MEMBER SET ...
```

위는 JPA를 사용하지않는 자바 + SQL 코드이다.

만약 여기서 전화번호 필드가 추가된다면?

```java
public class Member {
    private String memberId;
    private String name;
    private String tel;
    ...
}
```

```sql
INSERT INTO MEMBER ( MEMBER_ID, NAME, TEL ) VALUES
SELECT MEMBER_ID, NAME, TEL FROM MEMBER M
UPDATE MEMBER SET ... TEL = ?
```

이렇게 되면 **객체의 변경**과 **Query의 수정** 두 가지 작업을 모두 해주어야한다.

이는 매우 비효율적이다.

요구 사항은 계속 변한다.

소프트웨어는 계속 변화하며 요구 사항은 어제와 오늘이 다르다.

객체 또한 계속해서 진화하고 변화한다.

이러한 변화에 유연하게 대처하기 위해서 개발자들은 적은 시간에 뻐르게 기능을 추가하고 변경 할 수 있게 개발 해야한다.

관계형 DB에서는 필연적으로 SQL과 객체가 엮이기 마련이다.

## 객체와 관계형 데이터베이스의 차이

1. 상속

2. 연관 관계

   - 객체는 **참조**를, 테이블은 **외래키**를 사용한다.

3. 데이터 타입

4. 데이터 식별 방법

## SQL에 맞춰서 객체를 개발하기는 어렵다

왜냐하면 SQL을 기반으로 해서 객체답게 모델링을 하려면 너무 많은 코드와 작업이 필요로해진다.

사람들은 객체를 자바 컬렉션에 저장 하듯이 DB에 저장할 수 없을까? 라는 고민을 하기 시작한다.

## JPA의 등장

- 자바 진영 ORM 표준

### ORM

- Object-Relational Mapping (객체 관계 매핑)
- 객체는 객체대로 설계
- ORM 프레임워크가 중간에서 매핑
- 대중적인 언어에는 대부분 ORM 기술이 존재

## JPA는 애플리케이션과 JDBC 사이에서 동작

Java 애플리케이션 <-> JPA <-> JDBC API <-> DB

## 저장은 어떻게

유저가 Entity를 저장하는 메소드를 사용하면 JPA가 Entity 객체를 분석하여 적절한 INSERT 쿼리를 만들어내고 JDBC API를 사용해 DB에 저장함.

JPA는 결국 패러다임의 불일치를 해결해준다.

## 조회는 어떻게

JPA가 멤버 객체를 보고 적절한 SELECT 쿼리를 만들어낸다.

ResultSet을 객체에 매핑한 후 객체에 매핑해서 반환해준다.

이 역시도 패러다임의 불일치를 해결해준다.

## JPA 소개

EJB (틀딱 자바 표준) -> 하이버네이트(오픈소스) -> JPA (자바 표준)

### 자바 진영 ORM의 역사

EJB가 자바 표준으로 만들고 영업을 오지게 잘했으나 직접 써보면 개구림, 성능도 안나옴.

기술 자체가 아마추어적이고, 인터페이스도 엄청나게 구현해야함. 기능도 잘 안동작함.

이 EJB를 쓰던 SI 개발자 개빈 킹이 **내가 만들어도 이거보단 낫겠다**하고 만든게 그 유명한 **Hibernate**다.

EJB가 망하고 하이버네이트가 쫙 든다.

자바 진영은 크게 반성하고 **Hibernate**를 만든 개빈 킹을 잡아와서 복사 붙여넣기 수준으로 만든게 JPA다.

하이버네이트가 오픈소스이므로 JPA가 하이버네이트를 거의 가져다 왔지만 자바 진영의 표준이 될 때는 어느 정도 다듬게 된다.

### 또 하나의 재밌는 이야기

EJB에서도 지금의 스프링 IoC 컨테이너와 유사한 기능이 있었지만 너무 너무 구렸었다.

이 때 역시나 또 한명의 SI 개발자가 EJB를 보고 **이건 아니다** 싶어서 만든게 바로 지금 자바 진영의 애플리케이션 프레임워크 표준이라고 할 수 있는 **Spring Framework**이며 이 개발자가 바로 **로드 존슨**이다.

## JPA는 표준 명세

JPA는 사실상 인터페이스의 모음이다.

이 JPA 스펙을 구현한 몇 가지들이 있는데, 80~90%는 하이버네이트를 쓴다.

## JPA와 패러다임의 불일치 해결

<img width="754" alt="스크린샷 2020-11-28 오전 1 23 42" src="https://user-images.githubusercontent.com/43809168/100468763-65184e80-3118-11eb-96db-04feac813c1e.png">

객체 지향 언어에는 **상속**이 존재하지만 RDBMS에서 **상속**이란 개념은 존재하지 않는다.

그나마 비슷하게 흉내내볼만한 것이 바로 Table 슈퍼타입, 서브타입 관계이다.

JPA의 상속은 `jpa.persist(album)`으로 전달하면

JPA는

```sql
INSERT INTO ITEM ...
INSERT INTO ALBUM ...
```

과 같이 쿼리문을 생성해준다.

조회는 어떨까? `jpa.find(Album.class, albumId);`를 전달하면

```sql
SELECT I.*, A.*
FROM ITEM I
JOIN ALBUM A ON I.ITEM_ID = A.ITEM_ID
```

라는 쿼리를 생성해준다.

## JPA의 성능 최적화 기능

**JPA를 쓰면 기존 MyBatis나 SQL 기반 보다 성능이 더 떨어지지는 않을까**? 라고 걱정할 수 있다.

항상 소프트웨어에서는 계층 사이에 하나의 중간 계층이 생기면 **무언가** 할 수 있는 것이 생긴다.

**모아서 쏘는 버퍼링**과 **읽을 때 캐싱하는 것**을 할 수 있다.

이는 CPU와 메모리 구조도 마찬가지이다.

JPA에서도 마찬가지이다. JPA는 애플리케이션과 JDBC API 간의 징검다리 역할을 하기 때문에 잘만 쓴다면 성능을 더욱 끌어올릴 수 있다.

그 장점들을 지금부터 하나씩 살펴보자

### 1차 캐시와 동일성 보장

1. 같은 트랜잭션 안에서는 같은 엔티티를 반환한다. -> 약간의 조회 성능을 향상할 수 있다.

아래의 예시를 보자

```java
String memberId = "100";
Member m1 = jpa.find(Member.class, memberId); // SQL
Member m2 = jpa.find(Member.class, memberId); // 캐시

println( m1 == m2 ) // true
```

위와 같이 캐싱이 되어지고, **SQL은 한 번만** 실행한다.

굉장히 짧은 시간의 캐싱이라 실무에서 엄청나게 이점이 있다고 할 정도까지는 아니다.

2. DB Isolation Level이 Read Commit이어도 애플리케이션에서 Repeatable Read를 보장한다.

### 트랜잭션을 지원하는 쓰기 지연 - INSERT

1. 트랜잭션을 커밋할 때까지 INSERT SQL을 모음

2. JDBC BATCH SQL 기능을 사용해서 한번에 SQL 전송

JPA에서 옵션 하나만 켜주면, BATCH SQL 기능을 사용해 모아진 쿼리를 한방에 보낸다.

```java
transaction.begin(); // 트랜잭션 시작

em.persist(memberA);
em.persist(memberB);
em.persist(memberC);
// 여기까지 INSERT SQL을 DB에 보내지 않는다.

// 커밋하는 순간 DB에 INSERT SQL을 모아서 보낸다.
transaction.commit(); // 트랜잭션 커밋
```

### 트랜잭션을 지원하는 쓰기 지연 - UPDATE

1. UPDATE, DELETE로 인한 ROW락 시간 최소화

2. 트랜잭션 커밋 시 UPDATE, DELETE SQL 실행하고, 바로 커밋

```java
transaction.begin(); // 트랜잭션 시작

changeMember(memberA;
deleteMember(memberB);
비즈니스로직 수행(); // 비즈니스 로직 수행 동안 DB 로우 락이 걸리지 않는다.

// 커밋하는 순간 DB에 UPDATE, DELETE SQL을 보낸다.
transaction.commit(); // 트랜잭션 커밋
```

### 지연 로딩과 즉시 로딩

지연 로딩 : 객체가 실제 사용될 때 로딩

즉시 로딩 : JOIN SQL로 한번에 연관된 객체까지 미리 조회

## Reference

인프런 김영한님의 자바 ORM 표준 JPA 프로그래밍 - 기본편
