---
layout: post
title: 기본값 타입
subtitle: ''
categories: framework
tags: jpa
comments: false
---

## JPA의 데이터 타입 분류

JPA는 크게 **엔티티 타입**과 **값 타입**으로 분류한다.

엔티티 타입은 `@Entity`로 정의하며, 데이터가 변해도 식별자로 지속해서 추적이 가능한 타입이다.

값 타입은 int, String 처럼 단순히 값으로 사용하는 자바 기본 타입이나 객체를 의미한다.

식별자가 없고 값만 있기 때문에 추적이 불가능하다.

## 값 타입의 분류

기본 값 타입 : 자바 기본 타입, 래퍼 클래스(Integer, Long), String

임베디드 타입 : 복합 값 타입. 좌표값을 Position이라는 클래스로 묶는 경우

컬렉션 값 타입 : 자바 컬렉션에 기본 값 타입이나, 임베디드 타입을 넣을 수 있다.

## 기본 값 타입의 특징

- 생명 주기를 엔티티에 의존한다.

- 값 타입은 공유될 수 없으며 사이드 이펙트를 발생시키지 않는다.

- 자바의 래퍼 클래스나 String 같은 클래스는 공유는 가능하지만 변경이 불가능하다.

## Reference

인프런 김영한님의 자바 ORM 표준 JPA 프로그래밍 - 기본편
