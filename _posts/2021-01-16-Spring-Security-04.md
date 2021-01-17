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

## SpringBootWebSecurityConfiguration

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

## WebSecurityEnablerConfiguration

또 하나 중요한 `WebSecurityEnablerConfiguration`에 대해서 살펴보자.

```java
@Configuration(proxyBeanMethods = false)
@ConditionalOnMissingBean(name = BeanIds.SPRING_SECURITY_FILTER_CHAIN)
@ConditionalOnClass(EnableWebSecurity.class)
@ConditionalOnWebApplication(type = ConditionalOnWebApplication.Type.SERVLET)
@EnableWebSecurity
class WebSecurityEnablerConfiguration {

}
```

얼핏 코드로 보면 딱히 아무것도 없는 것 같지만 `EnableWebSecurity` 내부를 살펴보면,

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@Documented
@Import({ WebSecurityConfiguration.class, SpringWebMvcImportSelector.class, OAuth2ImportSelector.class,
  HttpSecurityConfiguration.class })
@EnableGlobalAuthentication
@Configuration
public @interface EnableWebSecurity {

 /**
  * Controls debugging support for Spring Security. Default is false.
  * @return if true, enables debug support with Spring Security
  */
 boolean debug() default false;

}
```

- WebSecurityConfiguration.class
- SpringWebMvcImportSelector.class
- OAuth2ImportSelector.class
- HttpSecurityConfiguration.class

와 같은 클래스들을 포함하고 있는 것을 확인할 수 있다.

여기서 `WebSecurityConfiguration`의 내부 중 `springSecurityFilterChain`이라는 메소드가 빈을 등록하고 있는 것을 확인할 수 있다.

```java
 @Bean(name = AbstractSecurityWebApplicationInitializer.DEFAULT_FILTER_NAME)
 public Filter springSecurityFilterChain() throws Exception {
  boolean hasConfigurers = this.webSecurityConfigurers != null && !this.webSecurityConfigurers.isEmpty();
  boolean hasFilterChain = !this.securityFilterChains.isEmpty();
  Assert.state(!(hasConfigurers && hasFilterChain),
    "Found WebSecurityConfigurerAdapter as well as SecurityFilterChain. Please select just one.");
  if (!hasConfigurers && !hasFilterChain) {
   WebSecurityConfigurerAdapter adapter = this.objectObjectPostProcessor
     .postProcess(new WebSecurityConfigurerAdapter() {
     });
   this.webSecurity.apply(adapter);
  }
  for (SecurityFilterChain securityFilterChain : this.securityFilterChains) {
   this.webSecurity.addSecurityFilterChainBuilder(() -> securityFilterChain);
   for (Filter filter : securityFilterChain.getFilters()) {
    if (filter instanceof FilterSecurityInterceptor) {
     this.webSecurity.securityInterceptor((FilterSecurityInterceptor) filter);
     break;
    }
   }
  }
  for (WebSecurityCustomizer customizer : this.webSecurityCustomizers) {
   customizer.customize(this.webSecurity);
  }
  return this.webSecurity.build();
 }
```

이 빈이 바로, **스프링 필터 체인을 정의하는 빈인 것**이다.

우리는 필터 체인은 여러 개의 필터를 묶어놓은 것을 의미한다는 것을 이전 시간에 배웠다.

이 필터 체인이 `WebSecurityConfiguration`에 의해 등록되는 것이며 이는 다시 `EnableWebSecurity`에 의해 등록되어지며 `WebSecurityEnablerConfiguration`가 포함된 `SecurityAutoConfiguration`에 의해 자동 등록되는 것이다.

## Delegating Filter Proxy

그리고 이전 시간에 배웠던 `Delegating Filter Proxy` 또한 스프링 부트에서 빈이 자동 설정에 의해 등록되는데,

```java
@Configuration(proxyBeanMethods = false)
@ConditionalOnWebApplication(type = Type.SERVLET)
@EnableConfigurationProperties(SecurityProperties.class)
@ConditionalOnClass({ AbstractSecurityWebApplicationInitializer.class, SessionCreationPolicy.class })
@AutoConfigureAfter(SecurityAutoConfiguration.class)
public class SecurityFilterAutoConfiguration {

 private static final String DEFAULT_FILTER_NAME = AbstractSecurityWebApplicationInitializer.DEFAULT_FILTER_NAME;

  // Delegating Filter Proxy가 이 자동 설정에서 등록된다.
 @Bean
 @ConditionalOnBean(name = DEFAULT_FILTER_NAME)
 public DelegatingFilterProxyRegistrationBean securityFilterChainRegistration(
   SecurityProperties securityProperties) {
  DelegatingFilterProxyRegistrationBean registration = new DelegatingFilterProxyRegistrationBean(
    DEFAULT_FILTER_NAME);
  registration.setOrder(securityProperties.getFilter().getOrder());
  registration.setDispatcherTypes(getDispatcherTypes(securityProperties));
  return registration;
 }
  ...
```

그래서 우리는 스프링 부트에서 `Delegating Filter Proxy`를 명시적으로 빈으로 등록하지 않더라도 사용이 가능한 것이다.

그리고 이전 시간에 배웠듯이, 스프링 부트에서는 별 다른 설정을 하지 않더라도 유저의 기본 이름과 패스워드가 설정이 되는데, 어떻게 이것이 가능한지도 살펴보자.

## UserDetailsServiceAutoConfiguration

```java
@Configuration(proxyBeanMethods = false)
@ConditionalOnClass(AuthenticationManager.class)
@ConditionalOnBean(ObjectPostProcessor.class)
@ConditionalOnMissingBean(
  value = { AuthenticationManager.class, AuthenticationProvider.class, UserDetailsService.class },
  type = { "org.springframework.security.oauth2.jwt.JwtDecoder",
    "org.springframework.security.oauth2.server.resource.introspection.OpaqueTokenIntrospector" })
public class UserDetailsServiceAutoConfiguration {

 private static final String NOOP_PASSWORD_PREFIX = "{noop}";

 private static final Pattern PASSWORD_ALGORITHM_PATTERN = Pattern.compile("^\\{.+}.*$");

 private static final Log logger = LogFactory.getLog(UserDetailsServiceAutoConfiguration.class);

 @Bean
 @ConditionalOnMissingBean(
   type = "org.springframework.security.oauth2.client.registration.ClientRegistrationRepository")
 @Lazy
 public InMemoryUserDetailsManager inMemoryUserDetailsManager(SecurityProperties properties,
   ObjectProvider<PasswordEncoder> passwordEncoder) {
  SecurityProperties.User user = properties.getUser();
  List<String> roles = user.getRoles();
  return new InMemoryUserDetailsManager(
    User.withUsername(user.getName()).password(getOrDeducePassword(user, passwordEncoder.getIfAvailable()))
      .roles(StringUtils.toStringArray(roles)).build());
 }

 private String getOrDeducePassword(SecurityProperties.User user, PasswordEncoder encoder) {
  String password = user.getPassword();
  if (user.isPasswordGenerated()) {
   logger.info(String.format("%n%nUsing generated security password: %s%n", user.getPassword()));
  }
  if (encoder != null || PASSWORD_ALGORITHM_PATTERN.matcher(password).matches()) {
   return password;
  }
  return NOOP_PASSWORD_PREFIX + password;
 }

}
```

여기서 보면 `inMemoryUserDetailsManager`에서 `InMemoryUserDetailsManager`라는 클래스를 빈으로 등록하고 있는 것을 확인할 수 있다.

여기서 유저의 이름과 기본 비밀번호를 인코드 하여 저장하고 있는 것도 확인할 수 있다.

그리고 `getOrDeducePassword`에서 로거를 사용하여 (`logger.info(String.format("%n%nUsing generated security password: %s%n", user.getPassword()));`) 톰캣이 구동 시 로그를 찍어 초기에 설정된 유저의 비밀번호를 알려주고 있다.

당연히 `ConditionalOnMissingBean`에 등록된 `AuthenticationManager.class, AuthenticationProvider.class, UserDetailsService.class`이 클래스들이 빈으로 등록되어있다면 해당 자동 설정은 무시될 것이다.

## 커스터마이징

기존 스프링 부트의 자동 로그인 대신, `httpBasic`으로 인증을 해보자.

아래와 같이 `WebSecurityConfigurerAdapter`를 상속받아 빈으로 등록하면 된다.

```kotlin
@Configuration
class WebSecurityConfiguration : WebSecurityConfigurerAdapter() {
    override fun configure(http: HttpSecurity?) {
        http?.run {
            this.authorizeRequests()
                    .anyRequest()
                    .authenticated()
                    .and()
                    .httpBasic()
        }
    }
}
```

<img width="1162" alt="스크린샷 2021-01-17 오후 8 37 16" src="https://user-images.githubusercontent.com/43809168/104839390-20cd5180-5904-11eb-8c0c-a64bc1d86843.png">
<img width="622" alt="스크린샷 2021-01-17 오후 8 37 40" src="https://user-images.githubusercontent.com/43809168/104839393-22971500-5904-11eb-8373-4c14ecc98d98.png">

그러면 위와 같이 팝업창이 뜨는 것을 확인할 수 있다.

인증에 성공하면 Authorization 헤더에 `Base64`로 인코딩된 값이 들어있는데,

이 값을 디코딩 해보면,

<img width="439" alt="스크린샷 2021-01-17 오후 8 40 32" src="https://user-images.githubusercontent.com/43809168/104839405-3f334d00-5904-11eb-9086-62c7b9db8f2a.png">

놀랍게도 해당 유저의 name과 password가 그대로 노출되는 것을 확인할 수 있다.

즉, 이 방식은 **보안에 굉장히 취약한** 인증 방법이라고 할 수 있다.

그도 그럴 것이, 헤더에 고스란히 유저의 이름과 비밀번호를 노출하고 있으며, `Base64` 디코딩 사이트에서 디코딩만 하면 손쉽게 아이디와 비밀번호를 알아낼 수 있기 때문이다.

디버깅을 해보면 내부에서 어떤 일이 일어나고 있는지를 알 수 있는데,

<img width="1184" alt="스크린샷 2021-01-17 오후 8 43 57" src="https://user-images.githubusercontent.com/43809168/104839473-b963d180-5904-11eb-9d2e-cd2021cff5e0.png">

`UserNameAndPasswordAuthenticationFilter` 대신 `BasicAuthenticationFilter`가 사용되어지고 있는 것을 확인할 수 있다.

그 이유는 폼 로그인 대신에, `httpBasic` 인증을 사용하고 있기 때문이다.

<img width="1030" alt="스크린샷 2021-01-17 오후 8 46 25" src="https://user-images.githubusercontent.com/43809168/104839537-119ad380-5905-11eb-8461-631020dcde3d.png">

또한 `ExceptionTranslationFilter`의 경우에도 마찬가지로 기존에는 `LoginUrlAuthenticationEntryPoint` 였으나 지금은 `BasicAuthenticationEntryPoint`로 바뀐 것을 확인할 수 있다.

`LoginUrlAuthenticationEntryPoint`이 로그인 url로 리다이렉트 했다면,

`BasicAuthenticationEntryPoint`의 경우 헤더에 값을 채워서 응답을 반환해준다.

다음 시간에는 `HttpBasic Authentication`의 문제점에 대해서 이야기해보고, `Digest Authentication`을 적용해보자.

## Reference

[유데미 스프링 시큐리티 강의](https://www.udemy.com/course/spring-boot-security-and-oauth2)
