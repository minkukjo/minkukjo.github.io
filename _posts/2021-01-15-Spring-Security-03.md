---
layout: post
title: Delegating Filter Proxy 탐험
subtitle: ''
categories: framework
tags: security
comments: false
---

## Delegating Filter Proxy

**Delegating Filter Proxy**가 정말로 이론으로 배운 것 처럼 구현되어있는지 내부를 구경해보자.

```java
public class DelegatingFilterProxy extends GenericFilterBean {

 @Nullable
 private String contextAttribute;

 @Nullable
 private WebApplicationContext webApplicationContext;

    ...
```

**GenericFilterBean**을 상속받고 있는데 이전 시간에도 언급한적 있지만 이 필터는 스프링의 설정 정보를 포함하는 필터이자 추상 클래스이다.

지금부터 Intellij IDE의 디버그 모드로 이 **Delegating Filter Proxy**의 내부는 어떤 값을 갖고 있는지 확인해보려고 한다.

여러 메소드들을 포함하고 있는데 그 중에서 `doFilter` 메소드의 마지막에 브레이크 포인트를 걸어고 요청을 보냈다.

```java
...
@Override
 public void doFilter(ServletRequest request, ServletResponse response, FilterChain filterChain)
   throws ServletException, IOException {

  // Lazily initialize the delegate if necessary.
  Filter delegateToUse = this.delegate;
  if (delegateToUse == null) {
   synchronized (this.delegateMonitor) {
    delegateToUse = this.delegate;
    if (delegateToUse == null) {
     WebApplicationContext wac = findWebApplicationContext();
     if (wac == null) {
      throw new IllegalStateException("No WebApplicationContext found: " +
        "no ContextLoaderListener or DispatcherServlet registered?");
     }
     delegateToUse = initDelegate(wac);
    }
    this.delegate = delegateToUse;
   }
  }

  // Let the delegate perform the actual doFilter operation.
  invokeDelegate(delegateToUse, request, response, filterChain); // 브레이크 포인트
 }
```

 <img width="593" alt="스크린샷 2021-01-15 오전 12 26 09" src="https://user-images.githubusercontent.com/43809168/104611317-5554c980-56c8-11eb-9c54-ad7941a1633b.png">

위와같이 여러 클래스 속성들이 있지만 그 중에 delegate 속성이 눈에 띈다.

이 필터 체인을 확대해보면,

<img width="597" alt="스크린샷 2021-01-15 오전 12 29 03" src="https://user-images.githubusercontent.com/43809168/104611622-acf33500-56c8-11eb-9237-659a8cada549.png">

15개의 필터들이 존재하는 것을 확인할 수 있다.

우리는 이전 시간에 6개의 필터만 언급했는데 사실은 더 많은 필터들이 존재한다.

<img width="514" alt="스크린샷 2021-01-15 오전 12 30 24" src="https://user-images.githubusercontent.com/43809168/104611803-ddd36a00-56c8-11eb-8f9e-6a7bbfb9fba5.png">

살펴보면 우리가 이전 시간에 언급했던 필터들이 존재하는 것을 확인할 수 있다.

<img width="926" alt="스크린샷 2021-01-15 오전 12 32 50" src="https://user-images.githubusercontent.com/43809168/104612121-3440a880-56c9-11eb-936f-7fb3ea3449b5.png">

그 중에서도 우리가 이전시간에 **AuthenticationFilter**로 배웠던 필터가 **UserNamePasswordAuthenticationFilter** 필터라는 다소 긴 이름으로 존재하는 것을 볼 수 있다.

여기서 provider에 userDetailsService가 존재하는 것을 볼 수 있는데 그 구현체가 **InMemoryUserDetailsManager**인 것을 확인할 수 있다.

기본값은 인메모리이며 이전시간에 배웠듯이, Jdbc를 사용하여 DB에 인증 정보를 저장하거나 Ldap을 사용하여 저장하는 것도 가능하다.

## Reference

[유데미 스프링 시큐리티 강의](https://www.udemy.com/course/spring-boot-security-and-oauth2)
