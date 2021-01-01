---
layout: post
title: 객체지향 쿼리 언어 - 기본 문법
subtitle: ''
categories: framework
tags: jpa
comments: false
---

## JPA의 다양한 쿼리 방법들

- JPQL

- JPA Criteria

- QueryDSL

- 네이티브 SQL

- JDBC API 직접 사용

## JPQL

- JPA를 사용하면 엔티티 객체를 중심으로 개발해야 한다.

- 문제는 검색 쿼리

- 검색을 할 때도 테이블이 아닌 엔티티 객체를 대상으로 검색해야한다.

- 모든 DB 데이터를 객체로 변환해서 검색하는 것은 불가능하다.

- 애플리케이션이 필요한 데이터만 DB에서 불러오려면 결국 검색 조건이 포함된 SQL이 필요하다. ( where문이 조건을 만들고, 그룹핑을 해서 가져와야함 )

- JPA는 SQL을 추상화한 SPQL을 제공

## Query DSL

- 문자가 아닌 자바코드로 JPQL을 작성할 수 있음

- 사실상 JPQL 빌더

- 컴파일 시점에 문법 오류를 찾을 수 있다

- 동적쿼리 작성의 편리함

- 단순하고 쉽다

- 실무 사용 권장

## Reference

인프런 김영한님의 자바 ORM 표준 JPA 프로그래밍 - 기본편
