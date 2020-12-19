---
layout: post
title: 준영속 상태
subtitle: ''
categories: framework
tags: jpa
comments: false
---

## 준영속 상태 (detach)

- 영속 -> 준영속

- 영속 상태의 엔티티가 영속성 컨텍스트에서 분리 (detached)

- 영속성 컨텍스트가 제공하는 기능을 사용 못함 ( DirtyChecking, 업데이트 쿼리 발행 등)

**영속 상태란** 객체가 1차 캐시에 올라왔을 때 우리는 **영속성 컨텍스트에 의해 관리되는 객체**라고 표현한다.

## 준영속 상태로 만드는 방법

- em.detch(entity) : 특정 엔티티만 준영속 상태 전환

- em.clear() 영속성 컨텍스트를 완전히 초기화

- em.close() 영속성 컨텍스트 종료

## Reference

인프런 김영한님의 자바 ORM 표준 JPA 프로그래밍 - 기본편
