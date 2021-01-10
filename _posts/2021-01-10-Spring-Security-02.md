---
layout: post
title: Spring Security의 내부
subtitle: ''
categories: framework
tags: security
comments: false
---

## Spring Security Security의 내부

<img width="1172" alt="스크린샷 2021-01-10 오후 8 25 41" src="https://user-images.githubusercontent.com/43809168/104121491-046c6a80-5382-11eb-93c3-a9f8b8745cef.png">

## 첫 번째 시나리오

### 다음은 유저가 로그인 하기 전에 Unsecured Resouce에 접근하려고 할 때 일어나는 시나리오이다

1. 유저가 요청을 보낸다.

2. **Security Context Persistence Filter**가 Session에 Security Context가 존재하는지 확인한다.

3. 인증되지 않은 유저이므로 비어있는 **Security Context** 객체를 생성하고 **Security Context Holder**에 집어넣는다.

4. 다음으로는 **Logout Filter**로 이동하는데 아무것도 하지 않는다.

5. 다음은 **Authentication Filter**로 이동한다. 유저는 로그인 하지 않았으므로 이 필터 역시 아무것도 안한다.

6. 다음은 **Remember me Filter**이다. Rember me 쿠키가 존재하는지를 본다. 로그인 안했으니까 당연히 이 쿠키도 없을 것이다. 그래서 이 필터도 아무것도 안한다.

7. 다음은 **Exception Traslation Filter**이다. 예외가 발생하지 않았기 때문에 넘어간다.

8. 다음은 **Filter Security Interceptor**이다. 우리의 홈페이지는 Not Secure하므로 요청을 해당 자원으로 전송되게 허락해준다. 그리고 홈페이지에 접근할 수 있을 것이다. 그리고 나면 그 컨트롤러는 다시 **Security Context Persistence Filter**로 요청을 돌려보낸다. **Security Context Persistence Filter**는 **Security Context**가 바뀌었나 확인하지만 아무것도 바뀌지않았고 여전히 비어있다. 그러면 **Security Context Holder**에서 **Security Context**를 제거한다.

## 두 번째 시나리오

### 이전 시나리오와는 반대로 유저가 로그인 하기 전에 Secured Resouce에 접근하려고 할 때는 어떤 일이 일어날까?

1. 유저가 요청을 보낸다.

2. 유저가 로그인 하기 전이니까 이전과 마찬가지로 **Security Context Persistence Filter**에서 비어있는 **Security Context** 객체를 생성하고 **Security Context Holder**에 집어넣는다.

3. 다음으로는 **Logout Filter**로 이동하는데 아무것도 하지 않는다.

4. 다음은 **Authentication Filter**로 이동한다. 유저는 로그인 하지 않았으므로 이 필터 역시 아무것도 안한다.

5. 다음은 **Remember me Filter**이다. Rember me 쿠키가 존재하는지를 본다. 로그인 안했으니까 당연히 이 쿠키도 없을 것이다. 그래서 이 필터도 아무것도 안한다.

6. 다음은 **Exception Traslation Filter**이다. 예외가 발생하지 않았기 때문에 넘어간다.

7. 다음은 **Filter Security Interceptor**이다. 여기서는 이전과 좀 다르다. 왜냐하면 이전에는 Unsecured Resouce이었기에 로그인 하지 않더라도 접근을 허락했지만, 지금은 Secured Resouce이기 때문에 여기서는 **Authentication Exception**을 발생시킨다.

8. 예외가 발생했으므로 **Exception Traslation Filter**가 해당 예외를 받으면 **Authentication Entry Point**에게 요청을 위임한다. **Authentication Entry Point**가 login URL로 사용자를 **Redirect** 시킨다.

## 세 번째 시나리오

다음은 유저가 로그인 페이지에서 유저 정보를 발송한 경우이다.

### 즉 유저가 로그인 페이지에서 ID, Password를 입력하고 서버로 전송했을 때의 시나리오이다

1. **Security Context Persistence Filter**는 역시 비어있는 **Security Context** 객체를 생성하고 **Security Context Holder**에 집어넣는다.

2. 다음으로는 **Logout Filter**로 이동하는데 아무것도 하지 않는다.

3. 다음은 **Authentication Filter**로 이동한다. 여기서 유저가 로그인을 했으므로 인증을 해준다. 그러기 위해 **Authentication Manager**라고 불리우는 인터페이스에 해당 요청을 위임한다.

4. **Authentication Manager**는 **authenticate**라는 메소드를 갖고 있으며 해당 메소드의 아규먼트는 **Authentication** 클래스이고 반환값 역시 **Authentication**이다.

5. **Authentication Manager**는 이러한 인증 객체를 직접 생성하지는 않는다. **Authentication Manager**는 다시 **Authentication Provider**에게 해당 인증 객체 생성을 위임한다. 이 Provider들이 실제로 인증을 진행한다.

6. **Authentication Provider**는 유저의 정보를 필요로하는데 가령 이름, 비밀번호 등과 같은 유저 정보들이다. **Authentication Provider**는 **User Detail Service**에게 해당 유저 정보를 가져다 줄 것을 위임한다.

7. **User Detail Service**는 DB에 접근하여 해당 유저의 인증 정보를 가져오고 **User Details** 객체를 **Authentication Provider**에게 반환해준다.

8. **User Detail Service**는 인터페이스이고 그 구현체로는 **InMemoryUserDetailsManager**, **JdbcUserDetailsService**, **LdapUserDetailManager**가 있다. 기본 값은 **InMemoryUserDetailsManager**이다.

9. 결과적으로 **Authentication Filter**는 유저 인증이 성공했으므로 **Security Context**에게 **Authentication** 객체로 채우며 **Authentication**는 **Principal**과 **Authorities**를 채운다.

10. **Security Context**는 쓰레드 로컬에서 관리되기 때문에 하나의 요청에 대한 진행이라면 어디서든 이 인증 정보를 가져다 쓸 수 있게 되는 것이다.

## 네 번째 시나리오

### 유저가 Admin 권한과 함께 로그인한 이후 Secured Resource에 요청을 한 경우

이 시나리오에서는 해당 페이지에는 **Admin** 권한을 가진 유저만 접근할 수 있다고 가정한다.

1. **Security Context Persistence Filter**는 **Http Session**에 **Security Context**가 있는지 확인한다. 여기서는 이미 로그인 된 이후이므로 **Security Context Holder**에 집어넣고 또한 **Thread Local**에도 집어넣는다.

2. 다음으로는 **Logout Filter**로 이동하는데 아무것도 하지 않는다.

3. 다음은 **Authentication Filter**로 이동한다. 이미 로그인 한 이후이므로 이 필터는 아무런 역할도 수행하지 않는다.

4. 다음은 **Remember me Filter**이다. 리멤버 쿠키를 사용하지는 않았으니까 아무것도 하지 않는다.

5. 다음은 **Exception Traslation Filter**이다. 예외가 발생하지 않았기 때문에 넘어간다.

6. **Filter Security Interceptor**는 해당 유저가 **Admin** 권한을 갖고 있는지 체크해야한다. 왜냐하면 해당 유저의 접근을 허락할지 안할지를 결정해야하기 때문이다.

7. **Filter Security Interceptor**는 유저의 권한 체크를 **Access Desicion Manager**라는 빈 객체에게 위임한다. **Access Desicion Manager**는 해당 유저가 **Admin** 역할을 갖고 있으므로 해당 자원에 대한 요청의 접근 권한을 부여하는 역할을 담당한다.

8. 그리고 나면 해당 요청은 해당 페이지(자원)에 접근이 가능해진다.

9. 그 후 다시 **Security Context Persistence Filter**로 돌아간다.

## 다섯 번째 시나리오

### 유저가 권한 없이 Secured Resource에 접근하려는 경우

1. **Security Context Persistence Filter**는 **Http Session**에 **Security Context**가 있는지 확인한다. 여기서는 이미 로그인 된 이후이므로 **Security Context Holder**에 집어넣고 또한 **Thread Local**에도 집어넣는다.

2. 다음으로는 **Logout Filter**로 이동하는데 아무것도 하지 않는다.

3. 다음은 **Authentication Filter**로 이동한다. 이미 로그인 한 이후이므로 이 필터는 아무런 역할도 수행하지 않는다.

4. 다음은 **Remember me Filter**이다. 리멤버 쿠키를 사용하지는 않았으니까 아무것도 하지 않는다.

5. 다음은 **Exception Traslation Filter**이다. 예외가 발생하지 않았기 때문에 넘어간다.

6. **Filter Security Interceptor**는 해당 유저가 **Admin** 권한을 갖고 있는지 체크해야한다. 왜냐하면 해당 유저의 접근을 허락할지 안할지를 결정해야하기 때문이다.

7. **Filter Security Interceptor**는 유저의 권한 체크를 **Access Desicion Manager**라는 빈 객체에게 위임한다. **Access Desicion Manager**는 해당 유저가 **Admin** 역할을 갖고 있으므로 해당 자원에 대한 요청의 접근 권한을 부여하는 역할을 담당한다.

8. 해당 유저는 **Admin** 권한을 갖고 있지 않으므로 **Access Desicion Manager**는 **Access Denied Exception**을 발생시킨다.

9. 발생한 예외는 **Exception Traslation Filter**로 전파된다.

10. 그리고 나서 **Exception Traslation Filter**는 요청이 거부되었다는 **Error Page**를 리턴한다.

## Reference

[유데미 스프링 시큐리티 강의](https://www.udemy.com/course/spring-boot-security-and-oauth2)
