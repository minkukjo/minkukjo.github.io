---
layout: post
title: 엔티티 매핑
subtitle: ''
categories: framework
tags: jpa
comments: false
---

## 엔티티 매핑

객체와 테이블 매핑 : `@Entity` `@Table`

필드와 컬럼 매핑 : `@Column`

기본 키 매핑 : `@Id`

연관관계 매핑 : `@ManyToOen`, `@JoinColumn`

## @Entity

이 애노테이션이 붙은 클래스는 JPA가 관리, 엔티티라 한다.

### 주의할 점

- 기본 생성자 필수

- final 클래스, enum, interface, inner 클래스 사용 X

  - 코틀린에서는 모든 클래스가 final이므로 allopen 컴파일러 플러그인을 사용해 모든 entity를 open으로 바꿔준다.

- 저장할 필드에 final 사용 X

## Reference

인프런 김영한님의 자바 ORM 표준 JPA 프로그래밍 - 기본편
