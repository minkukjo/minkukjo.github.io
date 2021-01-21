---
layout: post
title: OAuth 2.0이란?
subtitle: ''
categories: framework
tags: security
comments: false
---

## OAuth 2.0 이란?

OAuth 2.0은 현대의 인증 프로토콜로 사용되는 인증 프로토콜의 한 종류이다.

자세한 설명은 [OAuth 2.0 공식 사이트](https://oauth.net/about/introduction/)에 잘 나와있다.

그 중에서 OAuth 2.0을 가장 쉽게 이해할 수 있는 부분은 아래 대목

```
What is it For?
Many luxury cars today come with a valet key. It is a special key you give the parking attendant and unlike your regular key, will not allow the car to drive more than a mile or two. Some valet keys will not open the trunk, while others will block access to your onboard cell phone address book. Regardless of what restrictions the valet key imposes, the idea is very clever. You give someone limited access to your car with a special key, while using your regular key to unlock everything.
```

왜 OAuth 2.0을 사용하는지 발렛 파킹을 예시로 잘 설명되어있다.

## OAuth 2.0이 권한을 부여하는 방법

OAuth에서 Claim을 부여하는 방법들에 대하여 알아보자.

총 네가지 **Grant Type**이 존재한다.

1. Authorization Code Grant (가장 대중적인 방법)

2. Claimed Credentails Grant

3. Resource Owner Password Grant

4. Implicity Grant

## Authorization Code Grant가 동작하는 원리

**Resource Owner**가 **Resource Server**에 접근하고 싶다고 가정하자.

**Resource Owner**를 나 자신이라고 가정하고 이 **Resource Server**를 **op.gg**라고 가정하자.

여기서 우리는 별도의 회원가입을 하지 않고, **애플** 계정을 사용해 로그인 할 수 있고, **페이스북** 계정을 사용해 로그인 할 수 있다.

이제부터 OAuth 인증 과정을 `Step by Step` 알아보자..

1. op.gg 홈페이지에서 회원가입에서 **페이스북**을 선택한다.

2. 유저에게 **페이스북** 로그인 페이지로 리다이렉트 된다.

3. 페이스북의 로그엔 페이지에서 ID&Password(Credentail)을 입력한다.

4. 그리고 동의를 제출할 것이다. (대충 내가 이 사이트에 이런 범위의 내역을 줄것이라는...) 이 동의를 Scope이라고 부른다. 어느 범위까지 제공할 것인가에 대한 동의이다.

5. 로그인이 완료되면 Authorization Server가 DB를 뒤져서 해당 유저가 존재하는지 확인 후 클라이언트를 **Authorization Code**와 함께 원래 페이지로 리다이렉트 시킨다.

6. 클라이언트는 `Authorization Code`와 `Client ID` , `Client Secret`을 함께 전달한다.

7. 페이스북 인증서버가 브라우저에게 엑세스 토큰을 넘겨준다.

8. op.gg에 Profile을 토큰과 함께 요청한다.

9. op.gg는 페이스북에 토큰을 줘서 이 토큰이 유효한지 검사한다.

10. op.gg는 이 토큰이 유효한지 확인했으니 클라이언트(브라우저)에게 Profile을 전달한다.

11. 클라이언트는 profile을 전달받는데, 여기에는 이름, 전화번호, 이메일같은 정보들이 포함되어있다.

Spring Security에서는 마지막 과정의 Profile을 `Principal`에서 관리한다.

이러한 과정을 `Authorization Code Grant`라고 부른다.

## Reference

[유데미 스프링 시큐리티 강의](https://www.udemy.com/course/spring-boot-security-and-oauth2)
