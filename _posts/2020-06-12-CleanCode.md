---
layout: post
title: Clean Code 2장
subtitle: ""
categories: gd
tags: cc
comments: false
---

## 2장 의미있는 이름

의미있는 이름을 짓는 것은 중요하다.

오죽했으면 개발자들 사이에 이런 짤이 돌아다니겠는가?

![9966FD4A5C61785817](https://user-images.githubusercontent.com/43809168/84482103-1d207f00-acd2-11ea-92ea-1eb74d9c7845.jpeg)

이번 장에서는 어떻게 하면 **의미 있는 이름을 지을 수 있는가**에 대해서 설명한다.

## 의도를 분명하게 밝혀라

어떻게 의도를 분명하게 지을 수 있을까?

우선 다음과 같이 주석이 필요하다는 것 자체가 이미 의도를 분명하게 드러내지 못했다는 것을 뜻한다.

```kotlin
val d; // 경과 시간(단위 : 날짜)
```

d라는 이름에는 아무런 의미도 없다.

차라리 `int elapsedTimeInDays`가 더 낫다.

이처럼 의도를 드러내는 이름을 작성해야한다.

백문이불여일견 직접 보는게 최고다.

```kotlin
fun getThem(): List<IntArray> {
    val list1 = mutableArrayOf()
    for( x in theList) {
        if(x[0] == 4)
            list1.add(x)
    }
    return list1
}
``` 

위의 코드를 처음에 보면 무슨 생각이 드는가?

**대체 뭐하는 코드지?**라는 의문이 생기는게 정상일 것이다.

함수 이름도 모호하고 변수들의 이름도 모호하다.

게다가 x의 첫 번째 원소가 4인 경우의 조건도 이해가 안된다.

theList또 무엇이며 반환되는 list1은 또 뭐란 말인가?

이처럼 변수명을 제대로 짓지 못하면 코드를 이해하는게 어려워진다.

지뢰 찾기 게임을 만든다고 가정해보자.

theList는 게임판이 될 것이다.

4는 깃발이 꽂힌 상태를 의미하게 될 것이다.

이러한 가정을 바탕으로 코드를 조금 더 직관적으로 바꾸어보자.

```kotlin
fun getFlaggedCells(): List<IntArray> {
    val flaggedCells = mutableArrayOf()
    for( cell in gameBoard) {
        if(cell[STATUS_VALUE] == FLAGGED)
            flaggedCells.add(cell)
    }
    return flaggedCells
}
``` 

어떤가 훨씬 직관적이지 않은가?

코드만 봐도 의도가 명확하다.

조금 더 개선해보자면 조건을 따로 함수로 빼서 FLAGGED라는 상수를 감추는 것도 좋은 방법이 될 수 있다.

```kotlin
fun getFlaggedCells(): List<IntArray> {
    val flaggedCells = mutableArrayOf()
    for( cell in gameBoard) {
        if( cell.isFlagged() )
            flaggedCells.add(cell)
    }
    return flaggedCells
}
``` 

이름만 고쳤을 뿐인데 함수가 하는 일을 이해하기 쉬워졌다.

## 그릇된 정보를 피하라

가능하다면 컨테이너 이름을 그대로 사용하는 것은 피하라(XXXList, XXXMap, XXXSet 등등)

보다 직관적인 이름, 가령 여러 계정을 묶어서 그룹으로 만든다고 하면

`accountGroup`, `bunchOfAccounts`로 하거나 차라리 `Accounts` 더 낫다.

또한 흡사한 이름을 사용하지 말자.

XYZControllerForHandlingOfStrings라는 이름과

XYZControllerForExceptionOfStrings라는 이름을 보라.

가독성이 굉장히 떨어지고 두 이름은 겁나게 비슷하다.

## 의미있게 구분하라

가령 a1,a2,a3같은 이름의 변수를 짓는 것은 절대로 좋지 못하다.

실제로 나는 알고리즘을 주로 풀었어서 저런 식의 네이밍을 주로 했었는데

실제 상용 소프트웨어에 저런 코드가 들어가면 가독성이 굉장히 떨어진다.

여기서는 불용어(noise word)에 대해서 언급하고 있다.

여기서 말하는 불용어란 중복을 의미한다.

가령 변수에 variable을 사용한다던지, 테이블에 table이라는 변수를 붙인다던지 하는 단어로 이름을 짓는 것을 말한다.

가령 moneyAccount라는 변수와 money라는 변수가 있다면 그 코드를 읽는 사람은 어떻게 두 변수가 다른지 이해하기 어렵다.

그러니까 연속된 숫자를 붙이는 이름이나 불용어의 사용을 금지하자.

## 발음하기 쉬운 이름

이건 좀 재밌는데, 가급적이면 발음하기 쉬운 이름으로 해야 부르기 쉽다는 이야기다.

조금 더 깊게 들어가보자면 필연적으로 다른 개발자들과 해당 코드에 대해서 논의가 오고갈텐데 그때 발음하기 어려운 이름이라면 아무래도 커뮤니케이션이 어려워질 수 있다. 

더 중요한 문제에 대해서 논의해야하는 자리에서 발음 때문에 방해가 된다면 얼마나 어이없을지 상상해보라.

## 검색하기 쉬운 이름

변수의 이름을 잘 지어놓아야 나중에 찾기도 편하다.

프로젝트가 커지고 많은 변수들이 생기면 나중에 해당 변수가 어디에 선언되어있는지에 대한 정보를 찾기 위해 우리는 IDE의 검색기능을 이용할텐데,

여기에 잘 검색이 되는 이름을 지어놓는 것도 중요하다.

## 인코딩된 이름

접두어(멤버변수라면 앞에 m_라는 prefix를 붙이는 관례)를 붙이는 것은 아주 구닥다리 네이밍이다.

그리고 인터페이스와 이 인터페이스를 구현하는 클래스가 있다면

인터페이스 이름은 ShapeFactory로, 구현체는 ShapeFactoryImpl 또는 CShapeFactory가 더 낫다. 개인적으로는 ShapeFactoryImpl가 더 낫지않나 싶은 생각이다.

## 기억력 자랑 금지

반복문 내부에서 i,j,k는 괜찮지만 (l은 절대 금지) 그 외에는 이런 식의 네이밍은 절대 금지다.

## 클래스 이름

명사나 명사구가 적합하다.

Customer, WikiPage 등이 적합하다.

Manager, Processor, Data, Info 등의 이름은 지양하라.

## 메서드 이름

동사나 동사구가 적합하다.

postPayment, deletePage, save 등이 좋은 예다.

`Accessor`라면 get을 이름 앞에,

`Mutator`라면 set을 이름 앞에,

`Predicate`라면 is를 이름 앞에 붙여주자.

## 기발한 이름은 피해라

코드로 웃기려고는 하지 말자.

일반적인 보편 타당한 이름으로 하자.

가령 kill()을 whack()이라고 한다던지 하는 것들 말이다.

## 한 개념에 한 단어를 사용하라

일관성 있는 단어를 쓰자.

가령 Controller와 Manager가 있다면 그 코드를 읽는 사람은 당황스러울 것이다.

결국 둘은 같은 기능을 수행하는 객체임에도 이름이 다르다는 이유만으로 혼란을 가져온다.

가급적이면 일관성 있는 어휘를 채택하자.

## 말장난은 금지

add라는 메소드가 기존 값 두개를 더하거나 이어서 새로운 값을 더한다고 가정하자.

새로 작성하는 메소드는 집합에 값 하나를 추가한다.

이 메소드에도 add라는 단어가 포함될 수 있을까?

하지만 이 새로운 메소드는 기존 add와 맥락이 다르다.

그러므로 append나 insert가 조금 더 적합하다.

만약 새 메소드를 add로 부르는 것이야 말로 말장난이 된다.

## 해법 영역에서 가져온 이름을 사용하자

코드를 읽을 사람도 프로그래머라는 사실을 기억하자.

그러므로 전산 용어, 알고리즘 이름, 패턴 이름, 수학 용어 등을 사용해도 괜찮다.

모든 이름을 domain에서 가져오는 것은 현명하지 못하다.

VISITOR 패턴에 익숙한 개발자들이 많으므로 AccountVisitor라는 이름은 금방 이해한다.

JobQueue도 마찬가지다. 프로그래머에게 익숙한 기술 개념은 아주 많다.

## 문제 영역에서 가져온 이름을 쓰자

적절한 프로그래머 용어가 없다면 문제 영역에서 이름을 가져오자.

문제 영역 개념과 관련이 깊은 코드라면 문제 영역에서 이름을 가져와보자.

## 의미있는 맥락을 추가하자

맥락이 없으면 이해하기 힘든 경우가 있다.

가령 firstName, lastName, street, houstNumber, city, state, zipcode라는 변수가 있다.

위 변수들은 주소라는 사실을 금방 알아차릴 수 있다.

하지만 특정 메소드에서 state라는 변수 하나만을 사용한다면?

state가 주소와 관련된 변수라는 것을 알아차리기 어렵다.

그래서 addr이라는 접두어를 추가해서 addrFirstNmae, addrLastName.. addrState 이런식으로 이름을 지으면 조금 더 직관적이다.

## 불필요한 맥락은 없애자.

고급 휘발유 충전소 Gas Station Deluxe라는 애플리케이션을 개발한다고 가정하자.

그렇다고 모든 클래스 이름 앞에 GSD를 붙이는 것은 결코 좋은 생각이 아니다.

가령 GSD 회계 모듈에 MailingAddress 클래스를 추가하면서 GSDAccountAddress로 이름을 바꿨다고 하자.

나중에 다른 고객 관리 프로그램에서 고객 주소가 필요하다면 GSDAccountAddress 클래스를 사용할까?

물론 일반적으로는 짧은 이름 보단 긴 이름이 좋다.

단 의미가 분명한 경우에만.

## 결론

좋은 이름을 선택하려면 설명 능력이 뛰어나야하고 문화적인 배경이 같아야한다.

물론 이것은 어렵지만 노력해야한다.

우리는 쉽게 읽히는 코드를 짜는데 집중해야한다.

코드 가독성을 높이는 것은 Clean Code로 가는 가장 첫 번째 Step이다.

## Reference

Clean Code - 로버트.C.마틴