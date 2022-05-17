---
layout: post
title: 시간 관련 트러블 슈팅
subtitle: ''
categories: framework
tags: jpa
comments: false
---

## 문제 파악

오늘은 회사에서 해결한 시간 관련 이슈에 대해서 이야기해보려고 합니다.

문제의 원인은 회사에서 사용자의 개인정보를 암호화해서 저장하는 로직이 있습니다.

이 암호화 알고리즘은 복호화가 가능한 알고리즘이었는데, 암호화에 사용되는 Key 값은 created_at으로 사용을 하였었습니다.

어느날 다른 팀원이 복호화가 안되서 이슈가 발생한다고 말씀해주셨고,

팀 리더이신 쉘든과 함께 문제를 해결해나가기 시작했습니다.

우선 쉘든은 로그를 찍어보는게 좋을 것 같다는 의견을 주셨고, 로그를 찍어 암호화에 사용된 Key 값과 복호화에 사용된 Key를 비교하였습니다.

그런데 여기서 놀랍게도 `암호화에 사용된 값과 DB에 저장된 값이 다른` 엄청난 사실을 알아차리게 되었습니다.

이 시간은 정확히 1초 차이였습니다.

## DB에 저장될 땐 왜 1초가 늘어요...?

DB에서 읽어왔을 때는 1초인데 저장하면 2초가 되는 아주 무시무시한 현상이었습니다.

일단 원인은 찾았지만 이러한 현상이 **왜 발생했는가**?에 대해선 알 수 없었습니다.

원인은 알았으니까 복호화할 때 created_at에서 1초 빼면 해결되지 않을까..? 라고 생각했지만,

어떤 데이터는 잘 복호화가 되는 반면, 또 어떤 데이터는 복호화가 잘 되지 않기 때문에 섣불리 1초만 땡겨서는 더 큰 문제를 만들 수도 있겠다는 생각이 들었습니다.

그래서 문제 해결도 문제 해결이지만, 정확하게 **왜 발생했는지**를 알아야 이 문제를 풀 수 있을 것 같다는 생각이 들었습니다.

해당 이슈가 발생하는 프로젝트의 코드와 MySQL 공식문서를 꼼꼼히 읽다보니 다음과 같은 사실을 알아낼 수 있었습니다.

```
# 대충 아래 공식 가이드 번역한 내용
MySQL은 TIME,DATETIME, TIMESTAMP에 소수점 초 지원을 제공하며, 반올림 처리 될 수 있습니다.
```

![176bc0cc6514bf215](https://user-images.githubusercontent.com/43809168/158431542-c3d1528e-cc1e-4618-950a-11ee5bb5b369.gif)

(아~ 젠장 이거구나)

created_at은 DateTime 타입으로 저장되어 있었고 반올림이 될 수 있는 상황이었습니다.

이제야 왜 어떤 데이터는 복호화가 잘 되고, 어떤 데이터는 복호화가 안되는지 명확히 알 수 있는 순간이었습니다.

그럼 **읽어올 땐 어떻게 또 제대로 ms 단위까지 읽어올 수 있었던 것인가**? 라는 의문이 생겼습니다.

그 의문에 대한 해답은 아래 `Temporal` 애노테이션이 범인이라는 것을 확신할 수 있게 해주었습니다.

```java
@Temporal(TemporalType.TIMESTAMP)
@Column("created_at")
private Date createdAt;
```

즉, 하이버네이트 DB에서 Read 해왔을 때는 **TIMESTAMP**로 읽어오기 때문에 ms 단위까지 제대로 읽어올 수 있었고,

이후 영속화 될 때는 DB의 컬럼 타입이 **DATETIME**이므로 바꿔서 들어가는 과정에서 반올림이 발생하여 시간 차이가 발생하는 것이었습니다.

## 문제 해결 방안들

찾아보니 MySQL의 경우 **Temporal**에 아무런 값을 넣지 않으면 **DATETIME**으로 설정되는 것으로 확인되었는데... 현재 설정은 **TIMESTAMP**로 잡혀있었습니다. (분명 이유가 있었겠죠.)

그렇다고 이제와서 **Temporal**을 수정하자니 다른 사이드 이펙트가 발생할 수 있을 것 같고, 그렇다고 DB 컬럼을 **TIMESTAMP**로 바꾸는 것도 운영중인 DB의 테이블이 수 십개인데 그걸 일일이 다 바꾸는 것도 좋은 방법은 아닌 것 같다고 생각했습니다.

그래서 우선은 원인은 알아내었으니, 기존 데이터들을 한 번 다 읽어와서 반올림 문제가 있는 데이터와 기존 데이터들을 배치를 한 번 돌려서 복호화 한 뒤 **created_at**이 없는 Key로 다시 암호화하여 저장하게 하였습니다.

문제의 원인과 해결을 하였지만, 이후에 이런 현상이 발생하게 하지 않으려면 어떻게 해야하나 싶은 고민이 생겨서 조금 찾아보니,

자바 8에서 제공하는 시간 관련 타입을 사용했으면 이런 현상을 진작에 막을 수 있지 않을까 라는 생각이 들었습니다.

아니면 아예 MS 소수점 단위를 잘라서 저장하는 방법도 괜찮았을 것 같습니다.

이런저런 문제 원인 분석과 해결 과정을 거치면서 이러한 일련의 과정을 혼자 아는 것 보다는 팀원들에게 공유하는게 좋겠다 싶어서 정리해서 사내 메신저로 공유 하였습니다.

<img width="310" alt="Screen Shot 2022-03-16 at 1 48 25 AM" src="https://user-images.githubusercontent.com/43809168/158433434-11aeb9e7-f710-47a5-b76a-d5eb384aa0e6.png">

## 마치며

간만에 블로그 글을 써보는 것 같습니다.

문제를 만나면 항상 머리가 어지럽지만, 파고들어가보면 나름의 그 이유가 있고 왜 발생했으며 어떻게 극복할 수 있는지를 알아가는 과정을 정말 즐거운 것 같습니다.

평소 친하게 지내는 개발자 동생의 명언을 끝으로 이번 글을 마무리해보려 합니다.

<img width="361" alt="Screen Shot 2022-03-16 at 1 42 49 AM" src="https://user-images.githubusercontent.com/43809168/158433879-a2b272b8-de93-4e6b-8008-9a6721ebcbe0.png">

## Reference

<https://dev.mysql.com/doc/refman/5.7/en/fractional-seconds.html>