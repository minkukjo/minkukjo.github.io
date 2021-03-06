---
layout: post
title: Effective Java 04 - item 17 -
subtitle: ''
categories: language
tags: java
comments: false
---

## 변경 가능성을 최소화하라

클래스를 불변으로 만들어 변경을 최소화하는 것이 좋다.

불변 클래스는 가변 클래스보다 설계하고 구현, 사용하기가 쉽고 오류가 생길 여지도 적으며 안전하다.

불변 클래스를 만들기 위한 규약은 아래와 같다.

```
1. 객체의 상태를 변경하는 메서드(변경자)를 제공하지않는다.

2. 클래스를 확장할 수 없도록 한다.

3. 모든 필드를 final로 선언한다.

4. 모든 필드를 private로 선언한다.

5. 자신 외에는 내부의 가변 컴포넌트에 접근할 수 없도록 한다.
```

객체가 불변한 것의 가장 큰 장점은 `Thread Safe` 하다는 점이다.

불변 객체는 방어적 복사도 필요없다는 결론으로 이어진다.

아무리 복사해봐야 원본과 똑같으니 복사의 의미가 없다. 그러니 불변클래스는 clone 메소드나 복사 생성자를 제공하지 않는 편이 좋다.

불변 객체는 자유롭게 공유할 수 있음은 물론이요, 불변 객체끼리는 내부 데이터를 공유할 수도 있다.

이런 불변 클래스에도 단점은 있으니, 값이 다르면 반드시 독립된 객체로 만들어야한다는 점이다.

값의 가짓수가 많은 경우라면 일일이 인스턴스로 만들어줘야하므로 비용이 꽤 비쌀 것이다.

불변 클래스는 이름 그대로 상속을 받지 못하게 해야하는데, final 클래스로 만드는 것 보다 더 유연한 방법이 있다.

바로, 모든 생성자를 private 혹은 package-private으로 만들고 첫 장에 배운 public 정적 팩터리 메서드를 제공하는 방법이다.

이러한 정적 팩터리 메서드를 제공하면 인스턴스 캐싱 기능을 추가해 성능을 끌어올리는 것도 가능할 것이다.

또한 setter를 만드는 것에 특별한 주의가 필요하다.

이는 DDD에서도 언급되는 부분인데, 클래스는 꼭 필요한 경우가 아니라면 불변인 편이 좋다.

불변 클래스는 장점이 더 많고, 단점이라곤 특정 상황에서의 성능 저하밖에 없다.

모든 클래스를 불변으로 만드는 것은 불가능하지만, 불변으로 만들 수 없는 클래스라도 변경할 수 있는 부분은 최소화하는 편이 좋다.

생성자는 불변식 설정이 모두 완료된, 초기화가 완벽히 끝난 상태의 객체를 생성해야한다.

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
