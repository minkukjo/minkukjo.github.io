---
layout: post
title: 값 타입의 비교
subtitle: ''
categories: framework
tags: jpa
comments: false
---

## 값 타입의 비교

값 타입은 인스턴스가 달라도 그 안에 값이 같으면 같은 것으로 봐야한다.

그래서 값 타입은 a.equals(b)를 사용해 동등성 비교를 해야한다.

값 타입은 equals()를 적절하게 재정의해주어야한다. (코틀린이라면 값 타입의 임베디드 타입 객체는 data 클래스를 쓰면 되겠다)

## Reference

인프런 김영한님의 자바 ORM 표준 JPA 프로그래밍 - 기본편
