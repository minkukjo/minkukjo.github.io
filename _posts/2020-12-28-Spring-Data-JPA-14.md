---
layout: post
title: 상속관계 매핑
subtitle: ''
categories: framework
tags: jpa
comments: false
---

## 상속관계 매핑

객체는 **상속**이 존재한다.

그러나 관계형 DB에서는 **상속**이라는 것이 존재하지 않는다.

그나마 상속관계와 유사한 형태가 바로 슈퍼타입, 서브타입 관계라는 모델링 기법이 있다.

상속관계 매핑이란 객체의 상속 구조와 DB의 슈퍼타입 서브타입 관계를 매핑하는 것을 의미한다.

## 조인 전략

 <img width="956" alt="스크린샷 2020-12-28 오후 9 43 04" src="https://user-images.githubusercontent.com/43809168/103215081-af7a2e80-4955-11eb-992f-b9e5b8b7d607.png">

ITEM 테이블의 서브 타입 데이터를 만든 후 조인으로 가져오는 것이다.

즉 ITEM의 PK를 FK를 각각 갖게 한 후 슈퍼 테이블의 조인을 하는 것이다.

Insert는 2번을 하고, Select는 조인을 해서 가져오는 것이다.

여기서 중요한 점은 ITEM 테이블에서 이 데이터가 어떤 서브타입의 데이터인지 알기 위해 **TYPE**이라는 컬럼을 추가로 둔다.

TYPE에는 ALBUM, MOVIE, BOOK 등이 올 수 있다.

## 단일 테이블 전략

<img width="922" alt="스크린샷 2020-12-28 오후 9 50 08" src="https://user-images.githubusercontent.com/43809168/103215431-a8075500-4956-11eb-9b41-6eb85fd4e308.png">

논리 모델을 하나의 통짜 DB에 다 때려넣는 방식.

## 구현 클래스마다 테이블 전략

<img width="922" alt="스크린샷 2020-12-28 오후 9 50 39" src="https://user-images.githubusercontent.com/43809168/103215458-ba818e80-4956-11eb-8b9d-f99c8e57a589.png">

구현하는 클래스를 테이블마다 구현해버리는 방법도 있다.

## 상속관계 매핑

객체는 상속을 지원하니까 다 똑같지만, DB 설계는 각각 다르다.

JPA에서는 위에서 언급한 방식을 구현하는 기능들을 지원해준다.

## 주요 애노테이션

`@Inheritance`가 바로 그 주인공이다.

`@Inheritance(strategy=InheritanceType.XXX)`

XXX에는 **JOINED**, **SINGLE_TABLE**, **TABLE_PER_CLASS**가 들어갈 수 있다.

각 값의 의미는 너무나도 명확하기에 따로 설명 안해도 될 것 같다.

또한 구분을 위한 컬럼을 명시하기 위해, `@DiscriminatorColumn(name ="DTYPE")`과 같은 애노테이션이 존재한다.

`@DiscriminatorValue("XXX")` 애노테이션도 존재한다.

이 애노테이션은 내가 `DiscriminatorColumn`에 넣을 값을 명시하기 위한 애노테이션이다.

이 애노테이션은 서브타입 Entity에 사용하게 된다.

## 조인 전략의 장단점

 <img width="956" alt="스크린샷 2020-12-28 오후 9 43 04" src="https://user-images.githubusercontent.com/43809168/103215081-af7a2e80-4955-11eb-992f-b9e5b8b7d607.png">

### 조인 전략의 장점

테이블이 정규화 된다.

외래 키 참조 무결성 제약조건의 활용이 가능하다.

저장공간이 효율화된다.

공통 컬럼들은 슈퍼 타입에 정의해두고 서브타입에서는 필요한 컬럼만 저장하면 되기 떄문.

### 조인 전략의 단점

조회시 조인을 많이 사용하기 때문에 성능이 저하될 수 있다.

또한 조회 쿼리가 복잡해질 수 있다.

그리고 **데이터 저장 시 INSERT SQL이 2번 호출**된다는 단점이 있다.

사실 조인하거나 INSERT 쿼리가 두번 나가거나 하는 것들은 크게 단점이 아니다.

가장 문제가 되는 것은 **테이블이 복잡해진다**는 점이다.

## 단일 테이블 전략

<img width="922" alt="스크린샷 2020-12-28 오후 9 50 08" src="https://user-images.githubusercontent.com/43809168/103215431-a8075500-4956-11eb-9b41-6eb85fd4e308.png">

### 단일 테이블의 장점

조인이 필요없으므로 성능이 빠르다.

조회 쿼리가 단순하다는 장점도 있다.

### 단일 테이블의 단점

부모 컬럼을 제외한 자식 엔티티의 컬럼들은 모두 **NULLABLE**이 되어야한다.

그리고 단일 테이블에 모두 저장하므로 테이블이 커질 수 있고 상황에 따라서는 조인 전략보다 성능이 오히려 느려질 수 있다.

## 구현 클래스마다 테이블 전략

<img width="922" alt="스크린샷 2020-12-28 오후 9 50 39" src="https://user-images.githubusercontent.com/43809168/103215458-ba818e80-4956-11eb-8b9d-f99c8e57a589.png">

### 한줄 요약 : 이 전략은 쓰면 안된다

### 구현 클래스마다 테이블 장점

**이 전략은 DB 설계자와 ORM 전문가 둘 다 추천하지 않는다**.

서브 타입을 명확하게 구분할 수 있다.

not null 제약 조건이 가능하다.

### 구현 클래스마다 테이블 단점

여러 자식 테이블을 함께 조회할 때 성능이 느리다.

자식 테이블을 통합해서 쿼리하기가 어렵다.

## Reference

인프런 김영한님의 자바 ORM 표준 JPA 프로그래밍 - 기본편
