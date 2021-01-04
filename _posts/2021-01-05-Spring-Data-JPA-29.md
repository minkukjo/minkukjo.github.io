---
layout: post
title: Named Query
subtitle: ''
categories: framework
tags: jpa
comments: false
---

## Named Query란?

Entity에 `@NamedQuery` 애노테이션을 부여해서 이 쿼리를 이름으로 불러서 사용할 수 있다.

쉽게 말하면 미리 이름을 부여해서 재활용 할 수 있는 JPQL이다.

이는 동적 쿼리는 불가능하며 정적 쿼리만 가능하다.

## 왜 쓰지?

이 **NamedQuery**의 강력한 점은 애플리케이션 로딩 시점에 이 쿼리를 JPA가 파싱해서 캐싱 해놓는다.

그리고 **애플리케이션 로딩 시점에 쿼리를 검증**할 수 있다.

```java
@Entity
@NamedQuery(
    name = "Member.findByUserName",
    query = "select m from Member m where m.username = :username"
)
public class Member {
    ...
}
```

위와 같은 정적쿼리를 만들었을 때, 아래와 같이 사용가능하다.

```java
List<Member> results = em.createNamedQuery("Member.findByUserName", Member.class)
    .setParameter("username", "회원1")
    .getResultList();
```

만약 아래와 같이 쿼리를 잘못 작성한다면..?

```java
@Entity
@NamedQuery(
    name = "Member.findByUserName",
    query = "select m from MemberQQQQ m where m.username = :username"
)
public class Member {
    ...
}
```

위와 같은 에러는 애플리케이션 로딩 시점에 잡아낼 수 있다!

이것은 꽤 강력한 기능이다.

Spring Data JPA에서는 `@Query` 애노테이션을 사용할 수 있는데 이것은 `@NamedQuery`와 동일하다.

즉, 애플리케이션 로딩 시점에 쿼리문의 에러를 잡아줄 수 있다는 뜻이다.

즉, Spring Data JPA에서 `@Query`도 `@NamedQuery`로 등록된다는 뜻이다.

## Reference

인프런 김영한님의 자바 ORM 표준 JPA 프로그래밍 - 기본편
