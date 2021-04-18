---
layout: post
title: Effective Java 02 - item 03 -
subtitle: ''
categories: language
tags: java
comments: false
---

## 인스턴스화를 막으려거든 private 생성자를 사용하라

생성자를 명시하지않으면 컴파일러가 Default 생성자를 만들어주는 것은 자바 개발자라면 이미 다 알고 있는 사실이다.

여기서 이 객체가 정적 메서드와 정적 필드만을 담은 Util 클래스라면 인스턴스화를 굳이 할 필요가 없으므로 이를 막아야한다.

쉽게 막을 수 있는 방법은 `Private 생성자`를 선언하는 것인데, 가령 아래와 같다.

```java
public class UtilClass {
    private UtilClass() {
        throw new AssertionError();
    }
    ...
}
```

이 코드는 어떤 상황에서도 클래스가 인스턴스화 되는 것을 막아준다.

생성자가 분명히 존재하는데 호출 할 수 없으므로 적절한 주석을 달아줄 것을 저자는 명하고 있으나, 클린코드에서 주석은 가급적 지양하자고 했으니 난 안쓰겠다.

어쨌든, 이런식으로 생성자를 `Private`으로 만들어버리면 상속을 불가능하게 막는 효과도 있다.

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
