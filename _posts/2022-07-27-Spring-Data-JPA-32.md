---
layout: post
title: 하이버네이트는 Entity에 ID를 어떻게 넣어줄까?
subtitle: ''
categories: framework
tags: jpa
comments: false
---

## 개요

오늘 코드리뷰를 하던 중 같은 팀원과 Kotlin의 Id 컬럼이 붙은 프로퍼티를 `var`에서 `val`로 바꾸는게 어떻겠냐는 리뷰를 보게 되었다. ( 해당 PR은 다른 분의 PR 이었다. )

나는 하이버네이트가 `setter`를 호출할 것이라 생각해서 `var`를 쓰는게 맞지 않겠냐고 무식을 자랑하는 코멘트를 달았다.

그러자 동료분이 아래와 같은 리뷰를 남겨주셔서 한 수 배우는 계기가 되었다.

```
Hibernate는 reflection을 기반으로 값을 필드에 주입하기 때문에 

immutable하게 변수를 선언하더라도 값을 세팅하는데 이슈가 없는 것으로 알고 있습니다.
```

나는 여태 하이버네이트가 프록시 객체로 `Entity`를 만들고, `Entity`에 `setter`를 호출하여 쿼리 결과 값을 셋팅해준다고 생각했는데, 그게 아니었다.

얕은 지식으로 어설프게 아는 척하면 안되겠다는 좋은 교훈을 얻게 되었다는 결말이 되었으면 좋았겠지만 나의 호기심은 여기서 멈추지 않았다.

하이버네이트는 `대체 어디서 reflection을 해주는 걸까`?라는 ~~쓰잘데기없는~~ 의문이 계속해서 머릿속을 맴돌았고 이는 이 글을 쓰는 계기가 되었다.

구글링도 해보고 [관련 문서](https://developer.jboss.org/docs/DOC-15785#jive_content_id_But_Hibernate_uses_so_much_runtime_reflection)도 찾아봤는데, `어디서` `어떻게` 그 작업을 수행하는지에 대한 자세한 설명은 없었다.

그래서 하이버네이트 코드를 까서 **직접 찾아보기로 했다**.

## 준비

아래 예제는 회사 코드가 아닌, 별도의 프로젝트를 생성하여 테스트하였다.

<img width="571" alt="스크린샷 2022-07-27 오후 8 01 04" src="https://user-images.githubusercontent.com/43809168/181231619-23596d6f-3663-4975-9fb1-93d6eca7feb7.png">

우선 영속화될 대상인 `Entity`를 만들었고, `MySQL` DB를 사용한다 가정하였기에 PK 생성 타입은 `IDENTITY`로 하였다.

<img width="687" alt="스크린샷 2022-07-27 오후 8 02 22" src="https://user-images.githubusercontent.com/43809168/181231857-60ef53d4-149a-4f5f-a52a-7cbe3997c9cd.png">

그리고 애플리케이션이 구동되자마자 테스트할 수 있게 `Runner`를 하나 만들어줬다.

이제 `bookingRepository`가 `save`를 호출하면 `booking` entity에 ID 값이 채워질 것이다.

우리는 이 `ID` 값이 `어디서`, `어떻게` 채워지는지 추적해볼 것이다.

## 추적 시작

<img width="732" alt="스크린샷 2022-07-27 오후 8 03 54" src="https://user-images.githubusercontent.com/43809168/181232091-06f1fa83-f2ca-4319-b878-2768c9920e71.png">

처음 만나게 되는 코드는 `em`(EntityManager)의 `persist` 메소드이다.

참고로 `EntityManager`는 엔터티 관리를 담당하는 인터페이스이며 실제로 `persist`를 수행하는 구현체는 아래에 나올 `SessionImpl`이라는 클래스이다.

<img width="650" alt="스크린샷 2022-07-27 오후 8 05 21" src="https://user-images.githubusercontent.com/43809168/181232319-30a76d73-47f5-4528-aa20-ed87419d5401.png">

`EntityManager`는 `JPA`에서 추상화된 인터페이스이고, 실제 동작을 수행하는 것은 `SessionImpl`이라는 구현체가 담당한다.

이름에서 유추가 가능하지만, 저 `SessionImpl`은 `JDBC`를 래핑한 클래스이다.

그래서 `checkOpen`의 내부를 보면 `JDBC 커넥션`이 맺어져있는(Open된) 상태인지를 먼저 체크한다.

그 아래에 있는 `firePersist`의 이름만 봐도 뭔가 트리거를 발동시키는 메소드이름인데, `PersistEvent` 객체를 생성해 인자로 넘기는 것을 볼 수 있다.

<img width="737" alt="스크린샷 2022-07-27 오후 8 06 15" src="https://user-images.githubusercontent.com/43809168/181232463-f029896b-693b-4f74-9d9e-32abc1f07d25.png">

이벤트 리스너가 존재하고 이벤트 객체와 `PersistEventListener::onPersist` 메소드를 `fireEventOnEachListener`에 넘기는 것을 볼 수 있다.

<img width="729" alt="스크린샷 2022-07-27 오후 8 07 32" src="https://user-images.githubusercontent.com/43809168/181232700-df5df107-9315-4ef5-94f8-49498ace66be.png">

`PersistEventListener`의 `onPersist` 메소드를 추적해보자.

따라가보면, 우리가 이론으로 공부했던 `Entity` 생명주기들이 Enum으로 관리되는 것을 볼 수 있다.

앞서 호출한 `fireEventOnEachListener`를 호출하면서 넘긴 이벤트가 어떤 상태인지를 확인하고, 이벤트 상태에 따라 다른 동작을 수행한다.

여기서는 `TRANSIENT`이니 `TRANSIENT`의 로직을 타게 된다.

<img width="703" alt="스크린샷 2022-07-27 오후 8 09 13" src="https://user-images.githubusercontent.com/43809168/181232941-39a340f5-0889-4c7e-8fba-83857a448b5a.png">

`entityIsTransient` 메소드로 진입하면, 딱 봐도 뭔가 ID 만들어줄 것 같은 메소드가 보이기 시작한다.

`saveWithGeneratedId`로 진입해보자.

<img width="699" alt="스크린샷 2022-07-27 오후 8 09 42" src="https://user-images.githubusercontent.com/43809168/181233011-76a45ef2-7c78-41a3-b75d-0aeb0b4eafc3.png">

`AbstractSaveEventListener`의 `saveWithGeneratedId`이다.

```java
Serializable generatedId = persister.getIdentifierGenerator().generate( source, entity );
```

위 코드에서 해당 엔터티의 `generationId`라는 것을 얻어오는데, 정확한건 모르겠지만 아마도 어떤 방식으로 ID가 주입될 것인가를 결정하는 값임을 유추해볼 수 있다.

<img width="669" alt="스크린샷 2022-07-27 오후 8 10 41" src="https://user-images.githubusercontent.com/43809168/181233200-2035f33b-6637-4f83-bd0f-cd6a7f639da4.png">

`Booking` Entity의 경우 `generationId`가 `POST_INSERT_INDICATOR`로 나오게 된다.

Entity의 `GenerationType`은 DB의 생성방식을 따르는 타입이고, MySQL이어서 `POST_INSERT_INDICATOR`인 걸로 추측되나 정확하지는 않다.

<img width="728" alt="스크린샷 2022-07-27 오후 8 12 04" src="https://user-images.githubusercontent.com/43809168/181233421-8c502159-9600-4d7b-ac76-08f851ba903d.png">

`performSave`에서는 조건을 체크하고 `performSaveOrReplicate`로 이동하게 된다.

아직까지는 여전히 Entity의 ID가 0인것을 확인할 수 있다.

<img width="643" alt="스크린샷 2022-07-27 오후 8 12 38" src="https://user-images.githubusercontent.com/43809168/181233521-02fb9276-3d8a-45cd-b68f-30bd0c72e8e3.png">

`performSaveOrReplicate` 메소드를 조금 훑어보면 위와 같이 `addInsertAction`이라는 이름만 봐도 인서트 쿼리를 날릴 것 같이 생긴 코드가 등장한다.

여기가 실질적으로 DB로 insert 쿼리가 날아가는 곳이다.

내부에서는 `ActionQueue`라는 곳에 여러 `Action`(아마도 Select나 Update 같은)들을 담고 순차적으로 실행하기 위한 클래스로 보였다.

<img width="700" alt="스크린샷 2022-07-27 오후 8 13 15" src="https://user-images.githubusercontent.com/43809168/181233624-2884ea2b-ca16-488d-ac0e-f3ae56f6efd3.png">

이 `addInsertAction` 메소드를 타고 타고 들어가다보면 `execute`라는 메소드를 만나게 되는데 여기서,

```java
generatedId = persister.insert( getState(), instance, session );

...

persister.setIdentifier( instance, generatedId, session );
```

위 두 메소드를 만나게 된다.

하나는 MySQL에서 얻어온 ID 값을 가져오는 메소드이고,

다른 하나는 그 ID 값을 토대로 Entity의 ID 애노테이션이 붙은 필드나 세터에 값을 대입해주는 메소드이다.

<img width="708" alt="스크린샷 2022-07-27 오후 8 14 07" src="https://user-images.githubusercontent.com/43809168/181233776-2951bc00-fe31-4c08-a370-ee931bfe500f.png">

이 `setIdentifier` 내부에서 ```idSetter.set( entity, id, getFactory() );``` 이 코드가 idSetter를 사용해 id를 셋팅해주는 메소드이다.

여기까지만 봤을 때는 뭔가 setter를 호출할 것 같지만,

<img width="695" alt="스크린샷 2022-07-27 오후 8 14 50" src="https://user-images.githubusercontent.com/43809168/181233907-879d5557-552f-4968-88a8-7b048c0726df.png">

<img width="717" alt="스크린샷 2022-07-27 오후 8 14 58" src="https://user-images.githubusercontent.com/43809168/181233923-8fcb83da-9cdd-47c3-a728-e0ecb84fc77f.png">

~~응 리플렉션이야~~

실제로는 자바 `reflect` 라이브러리를 사용해 `field injection`하는 것을 확인할 수 있다.

<img width="718" alt="스크린샷 2022-07-27 오후 8 15 22" src="https://user-images.githubusercontent.com/43809168/181233995-f3d6b4d8-45b4-4eb0-8ca1-2a94c70e682d.png">

해당 `Action`이 끝나면 `Booking Entity`의 ID에 1이 정상적으로 들어온 것을 확인할 수 있었다.

코드 리뷰 덕에 몰랐던 사실을 알게 되어서 이를 알려준 동료 개발자님에게 다시 한 번 감사드리며 본 글을 마치도록 하겠다.

## 여담

네이버 예약팀에 와서 좋은 코드리뷰 문화를 경험해보고 있다.

단순히 코드리뷰가 잘못을 지적하는 것이 아닌, 이런식으로 지식 공유의 장(a.k.a 아고라)이 되어 같이 의논하고 지식을 나누는 점에서 참 좋은 문화를 가진 팀이라는 생각이 들었다.
