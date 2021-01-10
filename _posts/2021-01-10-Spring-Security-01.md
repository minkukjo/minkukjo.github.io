---
layout: post
title: Spring Security 기초
subtitle: ''
categories: framework
tags: security
comments: false
---

## Spring Security Security의 기초

Spring Security는 애플리케이션의 보안을 쉽게 구성할 수 있게 도와주는 프레임워크이다.

인증(Authentication)과 인가(Authorization)를 담당한다.

스프링 시큐리티는 이러한 인증과 인가를 **Filter**를 이용하여 구현한다.

이러한 인증의 흐름을 그림으로 표현하면 아래와 같다.

<img width="1488" alt="스크린샷 2021-01-10 오후 8 06 57" src="https://user-images.githubusercontent.com/43809168/104121129-70999f00-537f-11eb-8e6f-568dda5e672d.png">

시나리오의 흐름을 보면 다음과 같다.

1. 사용자가 인증을 요청하면 가장 먼저 DFP가 요청을 받는다.

2. DFP는 다시 FCP에게 요청을 전달한다.

3. FCP는 필터 체인에게 요청을 전달한다.

4. 정의된 필터들에 따라 인증 과정이 진행된다.

5. 이 때 필터는 Security Context 객체를 보면서 해당 유저의 인증이 되었는지를 확인한다.

6. 아래(Principal, Authorization) 객체들이 생성되고 Authentication 객체에 채워지며, Security Context에 다시 Authentication 객체가 채워지고, Http Session에 Security Context가 채워지는 식이다.

## Reference

[유데미 스프링 시큐리티 강의](https://www.udemy.com/course/spring-boot-security-and-oauth2)
