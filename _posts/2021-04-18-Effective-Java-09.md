---
layout: post
title: Effective Java 02 - item 07 -
subtitle: ''
categories: language
tags: java
comments: false
---

## 다 쓴 객체 참조를 해제하라

C, C++ 처럼 가비지 콜렉터가 없고 메모리를 직접 관리해야하는 언어로 개발을 하다가 자바로 넘어오면 그야말로 신세계다.

그래서 개발자들은 자칫 메모리 관리를 안해도 된다고 오해할 수 있으나 이는 사실이 아니다.

메모리 누수( OOM )과 같은 현상은 자바에서 종종 접하게 되는 이슈이며 이를 잘 관리해줘야한다.

그러나 가비지콜렉터를 지원하는 언어에서 이런 메모리 누수는 찾기가 어렵다.

그래서 사용이 끝나면 변수에 `data=null`을 넣음으로써 참조를 끊어주게 되면, 해당 인스턴스는 그 즉시 가비지 컬렉터의 수집 대상이 되므로 손쉽게 메모리 회수가 가능하다.

자기 메모리를 직접 관리하는 클래스라면 프로그래머는 항상 메모리 누수에 주의해야한다.

캐시 역시 메모리 누수를 일으키는 주범이다.

캐시가 필요한 상황이라면 `WeakHashMap`을 사용해 캐시를 만들어보자.

다 쓴 엔트리는 그 즉시 자동으로 제거될 것이다.

메모리 누수의 또 다른 주범은, 리스너 또는 콜백이라 부르는 친구들이다.

클라이언트가 콜백을 등록해두고 명확하게 해지하지않고 놔두면 계속해서 콜백이 쌓이게 된다.

이 때 콜백을 `weak reference`로 저장하면 가비지 컬렉터가 그 즉시 수거해가므로 예방해볼 수 있다.

에를 들어, `WeakHashMap`에 키로 저장하면 된다.

이러한 메모리 누수는 철저한 코드리뷰나 힙 프로파일러와 같은 디버깅 도구를 동원해야만 발견되기도 한다.

그래서 이런 종류의 문제는 예방법을 미리 익혀두는 것이 좋다.

## Reference

<img width="360" alt="스크린샷 2021-04-16 오후 4 24 23" src="https://user-images.githubusercontent.com/43809168/114987533-3e449400-9ed0-11eb-9b5f-a24f73b6f138.png">

이펙티브 자바 Effective Java 3/E

조슈아 블로크
