---
layout: post
title: Fetch Join - 특징과 한계
subtitle: ''
categories: framework
tags: jpa
comments: false
---

## Fetch Join의 특징과 한계

### 페치 조인에는 별칭을 줄 수 없다. (하이버네이트는 가능)

```sql
select t from Team t join fetch t.members # as m
```

왜냐하면, 페치조인은 기본적으로 연관된 엔티티를 다 가져오는 것이다.

만약 팀 하나에 멤버가 5명있는데 3명만 조회했다고 해보자.

그 3명만 따로 조작한다는 것은 굉장히 위험할 수 있다.

```sql
select t from Team t join fetch t.members as m where m.age > 10;
```

위 쿼리는 1개의 팀에 3명의 멤버만 조회될 수 있는데 이는 JPA에서 의도한 Fetch Join의 의도와 어긋나게 된다.

이는 JPA의 설계 사상인 객체 그래프 탐색과도 어긋날 수 있는 부분이다.

그래서 Fetch Join에서 별칭은 가급적 쓰지 말도록 하자.

### 둘 이상의 컬렉션은 Fetch Join 할 수 없다

1:N도 데이터가 뻥튀기될 수 있는데 1:N:N이 된다면..?

데이터가 예상하지 못하게 팍팍 늘어나면서 문제가 발생할 수 있다.

### 컬렉션을 Fetch Join하면 페이징 API를 사용할 수 없다

1:1이나 N:1같은 단일 값 연관 필드들은 Fetch Join도 페이징이 가능하다.

그런데 1:N에서는 페이징이 불가능하다.

잘 생각해보면 알 수 있는데 만약 한 팀에 2명의 멤버가 있다고 가정해보자.

페이징이 가능하다면, 페이지에서 조회할 수 있는 데이터를 1개로 설정하면 한 팀에 1명의 멤버만 조회가 될 것이다.

이는 논리적으로 맞지 않는다.

1:N에서 페이징 API를 사용할 수 있기는 한데, 하이버네이트에서 경고 로그를 남기고 메모리에서 페이징을 하는데 매우 위험할 수 있다.

그럼 어떻게 해결할까? 이를 해결하는 방법은 의외로 간단하다.

**그냥 뒤집으면 된다**.

```sql
select m from Member m fetch join Team
```

위와 같은 방식으로 뒤집어서 조회를 하면 다대일이 되므로 페이징 API를 사용할 수 있을 것이다.

그리고 이때 사용할 수 있는 옵션이 `@BatchSize` 애노테이션이다.

이 배치 사이즈는 1:N 관계일 때 엔티티를 최초 사용 시점에 DB에서 N에 해당하는 엔티티를 몇 개 만큼 가져올지 정의하는 옵션이다.

`hibernate.default_batch_fetch_size`으로도 설정이 가능하며 컬렉션 멤버 (`@OneToMany`)에 `@BatchSize` 애노테이션으로도 그 값을 정의할 수도 있다.

## 페치 조인 정리

모든 것을 페치 조인으로 해결할 수는 없다.

만약 여러 테이블을 조인해서 엔티티가 가진 모양이 아닌 전혀 다른 결과 (통계쪽)를 내야하는 경우라면 페치 조인 보다는 일반 조인을 사용하는 것이 낫다.

일반 조인을 사용하고 필요한 데이터만 조회해서 DTO로 반환하는 것이 효과적이다.

## Reference

인프런 김영한님의 자바 ORM 표준 JPA 프로그래밍 - 기본편