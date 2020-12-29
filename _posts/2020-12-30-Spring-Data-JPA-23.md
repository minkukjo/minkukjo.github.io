---
layout: post
title: 값 타입 컬렉션
subtitle: ''
categories: framework
tags: jpa
comments: false
---

## 값 타입 컬렉션

값 타입을 컬렉션에 담아서 사용하는 것을 의미한다.

## 값 타입 컬렉션 특징

- 값 타입 컬렉션도 지연 로딩 전략을 사용한다.

- 값 타입 컬렉션은 영속성 전에(Cascade) + 고아 객체 제거 기능을 필수로 가진다.

재밌는 점은 컬렉션의 값만 바뀌어도 DB 쿼리가 날아간다.

마치 영속성 전이처럼 말이다.

값 타입 컬렉션은 이 속성을 갖는 Entity에게 모든 의존성을 넘긴다.

## 값 타입 컬렉션의 사용법

```java
@Entity
public class Member {
    ...

    @ElementCollection
    @CollectionTable(name = "ADDRESS_HISTORY", joinColumns =
        @JoinColumn(name = "MEMBER_ID") // 외래키로 잡아준다.
    )
    private List<Address> addressHistory = new ArrayList<>();

    ...
}
```

`@ElementCollection`과 `@CollectionTable`을 사용한다.

컬렉션을 저장하기 위한 별도의 테이블이 필요하다.

## 값 타입 컬렉션을 사용할 떄 주의해야할 점

```java
Member member = new Member();
member.getAddHistory().add(new Address("old1","street","10000"));
member.getAddHistory().add(new Address("old2","street","10000"));

em.persist(member);
em.flush();
em.clear();

Member member = em.find(Member.class, member.getId());

findMember.getAddressHistory().remove(new Address("old1","street","10000")); // 삭제
findMember.getAddressHistory().add(new Address("newCity1","street","10000")); // 추가
```

위 코드의 출력될 쿼리를 예상해보자.

삭제라는 주석 코드에서 삭제 쿼리가 하나 날아갈거고,

추가라는 주석 코드에서 인서트 쿼리가 하나 날아갈 것이다.

그러나 실제로 하이버네이트는 **2번의 인서트 쿼리를 수행**한다.

이는 우리가 예상한 것과는 좀 다르다.

왜 그럴까?

결론적으로는 DB에 AddHistory에는 old2dㅘ newCity1이 추가되어있기는 하다.

값은 제대로 들어가긴 했는데, 인서트가 쿼리가 두 번 날아가는 것은 아무래도 이상하다.

## 값 타입 컬렉션의 제약사항

값 타입의 컬렉션에 변경 사항이 발생하면 주인 엔티티와 관련된 모든 데이터를 삭제하고, 값 타입 컬렉션에 있는 현재 값을 모두 다시 저장한다.

값 타입 컬렉션을 매핑하는 테이블은 모든 컬럼을 묶어서 기본 키를 구성해야한다.

## 그럼 어떡하지

결론적으로 이야기하자면 값 타입 컬렉션을 **쓰면 안된다**.

## 값 타입 컬렉션의 대안

실무에서는 값 타입 컬렉션 대신 일대다 관계를 고려하는게 낫다.

결국 일대다 관계를 위한 엔티티를 만들고, 여기에 값 타입을 사용한다.

그리고 Cascade와 고아 객체 제거를 사용해 값 타입 컬렉션 처럼 사용하면 된다.

## 정리

### 엔티티 타입의 특징

- 식별자 관리 가능

- 생명 주기 관리의 주체

- 공유 될 수 있음

### 값 타입의 특징

- 식별자 없음

- 생명 주기를 엔티티에 의존

- 공유하지 않는 것이 안전하다 (필요하다면 복사)

- 불변 객체로 만드는 것이 안전 ( e.q. Integer, String )

## 결론

엔티티와 값 타입을 혼동해 엔티티를 값 타입으로 만들면 안된다.

식별자가 필요해 지속적으로 추적, 변경해야한다면 그것은 값 타입이 아니라 엔티티이다.

## Reference

인프런 김영한님의 자바 ORM 표준 JPA 프로그래밍 - 기본편
