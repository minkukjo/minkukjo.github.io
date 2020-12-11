---
layout: post
title: 영속성 컨텍스트
subtitle: ''
categories: framework
tags: jpa
comments: false
---

## 영속성 관리

JPA를 이해하기 위해선 **영속성 컨텍스트**를 이해해야한다.

JPA에서 가장 중요한 2가지를 뽑자면,

- 객체와 관계형 데이터베이스 매핑 (ORM)
- **영속성 컨텍스트**

EntityManager Factory는 WAS에서 오직 **한 개만** 존재하며,

EntityManager Factory는 요청이 올 때 마다 **EntityManager**를 생성해준다.

**EntityManager**는 DB Connection을 하나를 할당받아서 DB와 대화를 한다.

## 영속성 컨텍스트

- JPA를 이해하는 핵심

- 엔티티를 영구 저장하는 환경

- **EntityManager.persist(entity)**

- 영속성 컨텍스트는 **논리적인 개념**

- 그래서 눈에 안보인다.

- 엔티티 매니저를 통해 영속성 컨텍스트에 접근

- EntityManager와 PersistenceContext가 1:1로 매핑된다.

## 엔티티의 생명주기

- new/transient - 영속성 컨텍스트와 전혀 상관없는 비영속 상태

- managed - 영속성 컨텍스트에 의해 관리되는 상태

  - 영속 상태가 되는 시점에 DB에 쿼리가 날아가지 않는다.
  - Transaciton이 commit하는 시점에 커밋이 날아간다.

- detached - 영속성 컨텍스트에 저장되었다가 분리된 상태

- removed - 삭제된 상태

## 영속성 컨텍스트의 이점

- 1차 캐시

- 동일성 보장

- 트랜잭션을 지원하는 쓰기 지연

- 변경 감지(Dirty Checking)

- 지연 로딩(Lazy Loading)

결국 WAS와 DB 사이에 또 다른 하나의 **중간 매개체**가 존재하는데 이것이 바로 **영속성 컨텍스트**이다.

두 계층 사이에 하나의 레이어가 추가된다는 것은 버퍼링을 하거나, 캐싱이 가능하다는 점이다.

## Reference

인프런 김영한님의 자바 ORM 표준 JPA 프로그래밍 - 기본편
