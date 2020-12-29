---
layout: post
title: 영속성 전이(CASCADE)와 고아 객체
subtitle: ''
categories: framework
tags: jpa
comments: false
---

## 영속성 전이 : CASCADE

특정 엔티티를 영속 상태로 만들 때 연관된 엔티티도 함께 영속 상태로 만들고 싶을 때 사용한다.

부모 엔티티를 저장할 때 자식 엔티티와 함께 저장한다.

주의해야할 점은 연관 관계나 즉시 로딩 이런 것들과 **아무 관계가 없다**.

## 영속성 전이 저장

```java
@OneToMany(mappedby = "team", cascade = CascadeType.ALL)
...
```

이 옵션은 간단하다. 결국 부모를 저장할 때 연관된 친구들도 같이 영속성 컨텍스트에서 관리해주게 하는 옵션이다.

## 주의할 점

주의할점은 이 옵션은 연관관계 매핑하는 것과 아무 관계가 없다.

## CASCADE 종류

**ALL** : 모두 적용

**PERSIST** : 영속

**REMOVE** : 삭제

## 고아 객체

부모 엔티티와 연관관계가 끊어진 자식 엔티티를 자동으로 삭제하는 기능이다.

옵션은 `orphanRemoval = true`

가령 다음과 같은 코드에서 발생한다.

```java
Parent parent = em.find(Parent.class, id);
parent.getChildren().remove(0);
// 자식 엔티티를 컬렉션에서 제거
// -> Delete 쿼리 발생
```

## 고아 객체 주의 할 점

참조가 제거된 엔티티는 다른 곳에서 참조하지 않는 고아 객체로 보고 삭제하는 기능이다.

참조하는 곳이 **반드시 하나 일 때** 사용해야한다.

즉, 특정 엔티티가 개인 소유할 때 사용할 수 있는 옵션이다.

`@OneToOne`, `@OneToMany`만 가능하다.

개념적으로 부모를 제거하면 자식은 고아가 된다.

따라서 이 객체 제거 기능을 활성화하면, 부모를 제거할 때 자식도 함께 제거된다.

이는 마치 CascadeType.REMOVE처럼 동작한다.

## 영속성 전이 + 고아 객체, 생명주기

`CascadeType.ALL + orphanRemovel=true`

두 옵션을 모두 활성화하면 부모 엔티티를 통해 자식의 생명 엔티티의 생명 주기를 관리할 수 있다.

이는 **DDD의 Aggregate Root 개념을 구현할 때 유용**할 수 있다. (오 DDD가 여기서 나오다니!!)

## Reference

인프런 김영한님의 자바 ORM 표준 JPA 프로그래밍 - 기본편
