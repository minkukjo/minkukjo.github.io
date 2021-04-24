---
layout: post
title: Effective Java 03 - item 13 -
subtitle: ''
categories: language
tags: java
comments: false
---

## clone 재정의는 주의해서 진행하라

`Clonable` 인터페이스는 복제가능한 클래스임을 명시하는 인터페이스임에도, 그 목적을 이루지 못했다.

`clone` 메서드가 정의된 클래스는 `Object` 클래스이며 그 마저도 `protected` 접근제한자라는게 문제다.

그래서 `Clonable`를 구현한다고 해서 외부 객체에서 `clone` 메서드를 호출할 수 없다는 점이 아이러니한 점이다.

메서드 하나 없는 `Clonable` 인터페이스는 `Object` 클래스의 `protected` 메서드인 `clone` 메서드의 **동작 방식을 결정한다**.

`Clonable` 인터페이스를 구현한 클래스의 인스턴스에서 `clone` 메서드를 호출하면 그 객체의 필드를 하나하나 복사한 객체를 반환하며, `Clonable` 인터페이스를 구현하지않은 클래스의 인스턴스에서 호출하면 `CloneNotSupportedException`을 반환한다.

이는 인터페이스를 상당히 이례적으로 사용한 예라고 하며 저자는 따라하지 말것을 강조했다.

`Clonable`로 구현한 클래스는 `clone` 메서드를 `public`으로 제공하고 사용자는 당연히 복제가 제대로 이루어지리라 기대하게 된다.

그 기대에 대한 리턴은 **깨지기 쉽고**, **위험하고** **모순적인** 매커니즘이 탄생한다.

그 모순은 바로 **생성자를 호출하지 않고도 객체를 생성할 수 있게 되어버리는 것이다**.

예제를 봐보자.

```java
@Override
public PhoneNumber clone() {
    try {
        return (PhoneNumber) super.clone();
    } catch (CloneNotSupportedException e) {
        throw new AssertionError(); // 일어날 수 없는 일
    }
}
```

Object 클래스의 `clone`을 호출하면 `Object` 타입이 반환되지만, `PhoneNumber`의 `clone` 메서드는 `PhoneNumber`을 반환하게 했다.

이를 자바에서 `convariant return typing`이라고 부르며 한국말로는 어렵게 `공변 변환 타이핑`이라고 부른다.

의미는 재정의한 메서드의 반환 타입은 상위 클래스의 메서드가 반환하는 타입의 하위 타입일 수 있다는 것이다.

위 코드에서 사실상 예외는 일어나지 않는데 `clone` 메서드에서 예외를 던지다보니, 거추장한 코드가 되었다.

`Clonable`을 구현한 클래스를 복제하는 것은 얼핏 보면 굉장히 편리해보이기도 하다.

그러나 `HashTable`과 같은 자료구조에서 `clone`은 버킷마다 리스트를 새롭게 생성하고, 원본 데이터의 키-값 쌍을 복제본 테이블의 `put` 메서드를 활용해 복사해주어야하는데 꽤 번거롭다. (Deep Copy 이슈)

일단 왜 이런 작업을 해야하는가에 대해 의문을 품을 수 있다.

`clone` 메소드는 얼핏 보면 편리해보이지만, 사실은 쓰레드 안전하지 않으며 `Object`의 `clone` 자체가 동기화를 전혀 고려하지않았다.

그래서 `super.clone` 외에 별 다른 작업을 하지 않는다하더라도 `clone`을 오버라이딩해서 쓰레드 세이프하게 개발해주어야한다.

또한 `clone` 메소드 자체가 예외를 던지게끔 설계가 되어있다보니, 재정의하는 곳에서 `clone`의 예외를 없애주어야하며 `public` 접근제한자로 만들어주어야한다. (그래야 사용하기 편리하다.)

이를 요악해보면 `Object`의 `clone` 메서드는 어째, 잘못 만들어진 것 같아보이기도 하다.

저자 역시 `clone`을 사용하기 보다는 다른 방법으로 복사를 권장하고 있다.

`Cloneable`을 이미 구현한 클래스를 확장한다면 어쩔 수 없지만, 그렇지 않은 상황이라면 가급적 `복사 생성자`와 `복사 팩터리`를 고려하는 것이 좋다.

```java
public Yum(Yum yum) { ... }
```

```java
public static Yum newInstance(Yum yum) { ... };
```

복사 생성자와 그 변형인 복사 팩터리는 `Cloneable/clone` 방식보다 훨씬 더 나은 대안이다.

복사 생성자와 복사 팩터리는 해당 클래스가 구현한 `인터페이스` 타입의 인스턴스를 인수로 받을 수 있다는 강력한 강점이 있다.

복사 생성자/복사 팩터리의 더 정확한 이름은 `Conversion Constructor`와 `Conversion Factory`이다.

이를 이용해 클라이언트는 원본의 구현 타입에 얽매이지 않고, 복제본의 타입을 직접 선택할 수 있다.

저자 역시 새로운 인터페이스를 만들 때 `Cloneable`을 확장하지 말것을 강조하였고, 가급적이면 생성자와 팩터리를 이용하는 것이 최고라고 이야기하였다.

다만, 배열만큼은 `clone` 메서드를 사용하는 것이 예외적으로 가장 깔끔한 방법이라고 한다.

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
