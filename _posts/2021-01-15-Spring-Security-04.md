---
layout: post
title: Spring Security AutoConfigurations의 이해와 커스터마이징
subtitle: ''
categories: framework
tags: security
comments: false
---

## 이전 시간 복습

<img width="1483" alt="스크린샷 2021-01-10 오후 9 40 03" src="https://user-images.githubusercontent.com/43809168/104123093-6762ff00-538c-11eb-9e42-74c66ee318bb.png">

우리는 이전 시간까지 스프링 시큐리티의 내부가 어떻게 동작하는지를 이해했다.

이번 시간에는 스프링 시큐리티의 자동 설정이 어떻게 이루어지는가를 살펴보려고 한다.

<img width="572" alt="스크린샷 2021-01-15 오전 12 39 58" src="https://user-images.githubusercontent.com/43809168/104613067-32c3b000-56ca-11eb-9228-2e5935dc47c9.png">

위 경로로 들어가보면 자동 설정이 엄청나게 많은데, 이것들이 바로 스프링에서 자동 설정해주는 클래스들이다.

그 중에서도 시큐리티에 관련된 클래스들도 있는데 아래와 같다.

```

org.springframework.boot.autoconfigure.security.servlet.SecurityAutoConfiguration,\
org.springframework.boot.autoconfigure.security.servlet.UserDetailsServiceAutoConfiguration,\
org.springframework.boot.autoconfigure.security.servlet.SecurityFilterAutoConfiguration,\
org.springframework.boot.autoconfigure.security.reactive.ReactiveSecurityAutoConfiguration,\
org.springframework.boot.autoconfigure.security.reactive.ReactiveUserDetailsServiceAutoConfiguration,\
org.springframework.boot.autoconfigure.security.rsocket.RSocketSecurityAutoConfiguration,\
org.springframework.boot.autoconfigure.security.saml2.Saml2RelyingPartyAutoConfiguration,\
org.springframework.boot.autoconfigure.sendgrid.SendGridAutoConfiguration,\
org.springframework.boot.autoconfigure.session.SessionAutoConfiguration,\
org.springframework.boot.autoconfigure.security.oauth2.client.servlet.OAuth2ClientAutoConfiguration,\
org.springframework.boot.autoconfigure.security.oauth2.client.reactive.ReactiveOAuth2ClientAutoConfiguration,\
org.springframework.boot.autoconfigure.security.oauth2.resource.servlet.OAuth2ResourceServerAutoConfiguration,\
org.springframework.boot.autoconfigure.security.oauth2.resource.reactive.ReactiveOAuth2ResourceServerAutoConfiguration,\
```

하나씩 살펴보자.

## SecurityAutoConfiguration

```java
@Configuration(proxyBeanMethods = false)
@ConditionalOnClass(DefaultAuthenticationEventPublisher.class)
@EnableConfigurationProperties(SecurityProperties.class)
@Import({ SpringBootWebSecurityConfiguration.class, WebSecurityEnablerConfiguration.class,
  SecurityDataConfiguration.class })
public class SecurityAutoConfiguration {

 @Bean
 @ConditionalOnMissingBean(AuthenticationEventPublisher.class)
 public DefaultAuthenticationEventPublisher authenticationEventPublisher(ApplicationEventPublisher publisher) {
  return new DefaultAuthenticationEventPublisher(publisher);
 }

}
```

**SecurityAutoConfiguration**는 ConditionalOnMissingBean 애노테이션이 존재하기 때문에 **DefaultAuthenticationEventPublisher** 클래스가 있을 때만 등록되는 빈이다.

**DefaultAuthenticationEventPublisher**는 스프링 시큐리티 스타터를 의존성에 추가하면 만들어지는 클래스이다.

즉, 스프링 시큐리티 스타터를 의존성에 등록하면 관련 설정 빈들이 자동으로 IoC 컨테이너에 등록된다는 것을 의미한다.

이 클래스는 또한 **SpringBootWebSecurityConfiguration**를 임포트하고 있는데,

```java
@Configuration(proxyBeanMethods = false)
@ConditionalOnDefaultWebSecurity
@ConditionalOnWebApplication(type = Type.SERVLET)
class SpringBootWebSecurityConfiguration {

 @Bean
 @Order(SecurityProperties.BASIC_AUTH_ORDER)
 SecurityFilterChain defaultSecurityFilterChain(HttpSecurity http) throws Exception {
  http.authorizeRequests().anyRequest().authenticated().and().formLogin().and().httpBasic();
  return http.build();
 }

}
```

여기서는 **ConditionalOnDefaultWebSecurity**라는 애노테이션으로 등록되어 있는데,

```java
@Target({ ElementType.TYPE, ElementType.METHOD })
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Conditional(DefaultWebSecurityCondition.class)
public @interface ConditionalOnDefaultWebSecurity {

}
```

**DefaultWebSecurityCondition** 클래스가 존재해야지만 해당 빈도 등록이 된다는 의미이다.

그리고 **defaultSecurityFilterChain** 메소드는 스프링 시큐리티 자동 설정에 의해 추가되는 기본 필터체인들을 설정하는 빈이기도 하다.

보면 인증, 인가, 폼 로그인, http 기본 등의 설정을 셋팅하고 있는 것을 확인할 수 있다.

## Reference

[유데미 스프링 시큐리티 강의](https://www.udemy.com/course/spring-boot-security-and-oauth2)
