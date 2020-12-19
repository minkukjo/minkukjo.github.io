---
layout: post
title: 필드와 컬럼 매핑
subtitle: ''
categories: framework
tags: jpa
comments: false
---

## 매핑 애노테이션 정리

**@Column** : 컬럼 매핑

**@Temporal** : 날짜 타입 매핑

**@Enumerated** : enum 타입 매핑

**@Lob** : BLOB, CLOB 매핑

**@Transiect** : 매핑하고 싶지 않은 컬럼

## @Column에 적용 가능한 속성들

**name** : 이름, 기본값은 객체의 필드 이름이다.

**insertable, updatable** : 등록, 변경 가능 여부, 기본값은 둘 다 true

**nullable**(DDL) : null의 허용 여부. DDL 생성시 not null 제약 조건 추가

**unique**(DDL) : **@Table**의 uniqueConstraints와 같지만 한 컬럼에 간단히 유니크 제약조건을 걸 때 사용한다.

**columnDefinition**(DDL) : 데이터베이스 컬럼 정보를 직접줄 수 있다. 디폴트는 필드의 자바 타입과 방언 정보를 사용한다.

**length**(DDL) : 문자 길이 제약조건, String에만 사용되며 기본값은 255

**precision, scale**(DDL) : BigDecimal 타입에서 사용한다. precision은 소수점을 포함한 전체 자릿수를, scale은 소수의 자리수다. precision 디폴트는 19, scale은 2다.

## @Enumerated

자바 enum 타입을 매핑할 때 사용한다.

기본값은 `EnumType.ORDINAL`이다.

`EnumType.ORDINAL`은 enum의 순서 (정수)를 저장한다.

`EnumType.STRING`은 enum의 이름을 저장한다.

enum은 **ORDINAL**을 자제하고 **String** 타입을 권장하고 있다.

## @Temporal

날짜 타입 때문에 필요한데, 자바 8의 LocalDate와 LocalDateTime을 사용할 때는 생략 가능하다. (최신 하이버네이트는 지원)

## @Lob

DB BLOB, CLOB과매핑한다.

## Reference

인프런 김영한님의 자바 ORM 표준 JPA 프로그래밍 - 기본편
