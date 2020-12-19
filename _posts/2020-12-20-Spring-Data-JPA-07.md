---
layout: post
title: 데이터베이스 스키마 자동 생성
subtitle: ''
categories: framework
tags: jpa
comments: false
---

## 데이터베이스 스키마 자동 생성 - 속성

### hinernate.hbm2ddl.auto

- create : 기존 테이블 삭제 후 다시 생성 (DROP + CREATE)

- create-drop : create와 같으나 종료 시점에 테이블 DROP

- update : 변경분만 반영(운영 DB에는 사용하면 안됨)

- validate : 엔티티와 테이블이 정상 매핑되었는지만 확인

- none : 사용 안함

## 데이터베이스 스키마 자동 생성 - 주의할 점

개발 초기 단계는 create 또는 update

테스트 서버는 update 또는 validate

스테이징과 운영 서버는 validate 또는 none

## Reference

인프런 김영한님의 자바 ORM 표준 JPA 프로그래밍 - 기본편
