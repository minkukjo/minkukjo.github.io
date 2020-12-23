---
layout: post
title: 기본 키 매핑
subtitle: ''
categories: framework
tags: jpa
comments: false
---

## JPA의 기본 키 매핑 전략

JPA의 기본 키 매핑 전략은 총 네 가지이다.

- IDENTITY: DB에 위임, MYSQL

- SEQUENCE: DB 시퀀스 오브젝트 사용, ORACLE

  - @SequenceGenerator 필요

- TABLE : 키 생성용 테이블 사용, 모든 DB에서 사용

  - @TableGenerator 필요

- AUTO : 방언에 따라 자동 지정, 기본값

## Table 전략

- 키 생성 전용 테이블을 만들어 데이터베이스 시퀀스를 흉내낸다.

- 장점 : 모든 DB에서 적용 가능

- 단점 : 성능이 구림 (직접 DB 테이블을 다루니까)

## IDENTITY 전략

- DB에서 쿼리를 받으면 그때 id를 셋팅한다.

- 즉, ID 값을 알 수 있는 시점은 DB에 값이 들어가는 시점.

- 영속성 컨텍스트가 관리하려면 PK가 있어야해서 persist() 시점에 INSERT 쿼리를 날리고 SELECT 쿼리를 날려서 ID 값을 얻어온다.
  - SELECT 쿼리를 날리면 느릴거고, JPA에서 셀렉트 쿼리는 보이지 않는다. 왜일까?
  - JDBC 드라이버에는 INSERT 쿼리를 날린 후 바로 리턴 받는 경우에는 SELECT 쿼리를 날리지 않고도 값을 얻어올 수있게 최적화가 되어있다. (오오~!)

## SEQUENCE 전략

- DB의 시퀀스 오브젝트를 사용하여 ID 값을 가져온다.

- 시퀀스 오브젝트는 테이블마다 생성된다.

- call nest value for [시퀀스 오브젝트 이름]

- 하이버네이트에 이 시퀀스 오브젝트를 얻으면 쿼리가 날아가야하는데 느리지 않으려나? 심지어 여러개면 매번 저 쿼리를 날리는건가?

- JPA에서는 allocationSize라는 재밌는 옵션이 있는데 이 값이 Default가 50이다.
  - 이 allocationSize 사이즈만큼 JPA는 DB에다가 N개로 맞춰놓고 메모리에서 1씩 증가시켜가면서 쓰는 것이다.
  - 그리고 현재 증가된 ID가 N+1개가 되면 DB에 다시 N+N개로 셋팅해놓고 계속 쓰는 것이다!

## 권장하는 식별자 전략

- DB 기본 키 제약 조건은 null이면 안되고 유일해야하고 변하면 안된다. == DB PK의 조건

- 미래까지 이 조건을 만족하는 자연키는 생각보다 찾기가 어렵다. 대리키를 사용하자.

  - 대리키란 Generate Value (비즈니스와 상관없는 값)를 사용하자.
  - 주민등록번호도 기본 키로 적절하지 않다. 기본키를 바꾸는 것은 **정말 어마어마**한 비용이 든다. 선정에는 아주아주 신중해야한다!

- 권장 : Long 형 + 대체키 + 키 생성전략 사용

## Reference

인프런 김영한님의 자바 ORM 표준 JPA 프로그래밍 - 기본편
