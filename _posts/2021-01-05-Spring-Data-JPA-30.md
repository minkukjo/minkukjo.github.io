---
layout: post
title: 벌크 연산
subtitle: ''
categories: framework
tags: jpa
comments: false
---

## 벌크 연산

재고가 10개 미만인 모든 상품의 가격을 10% 상승하려면...

JPA의 변경 감지만으로 실행하기엔 너무 많은 SQL이 실행된다.

변경된 데이터가 100만건이라면 100만건의 Update SQL이 실행될 것이다.

JPA가 **Bulk**성 작업 보다는 실시간 데이터에 최적화가 되어있다.

## 벌크 연산 예제

쿼리 한 번으로 여러 테이블 로우를 변경한다.

```java
em.createQuery("update Product p set p.price = p.price * 1.1 where p.stockAmount < :stockAmount")
    .setParameter("stockAmount",10)
    .executeUpdate(); // 요기가 핵심
```

INSERT에서도 물론 이러한 기능을 제공한다.

## 벌크 연산시 주의할 점

벌크 연산은 영속성 컨텍스트를 무시하고 데이터베이스에 바로 쿼리를 날린다.

벌크 연산을 먼저 수행하고, 벌크 연산 수행 후 영속성 컨텍스트를 초기화하면 된다.

## Reference

인프런 김영한님의 자바 ORM 표준 JPA 프로그래밍 - 기본편
