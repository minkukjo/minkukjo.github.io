---
layout: post
title: GCP에서 MySql Docker를 VM으로 띄워보기
subtitle: ''
categories: devops
tags: pc
comments: false
---

## 토이프로젝트에서의 DB

GCP에서 제공하는 CloudSQL은 HA, 모니터링, 알람 등 여러가지 기능을 지원해준다.

하지만 **비싸다**.

그렇다. 클라우드 서비스를 사용하려면 **돈**이 든다. 아주 많이.

어디선가 본 웃긴 글이 있는데,

**클라우드 서비스는 여러분들이 삭제를 깜빡한 인스턴스에 대한 비용으로 운영됩니다.**

라는 내용의 글이었다.

때문에 토이프로젝트를 할 때 CloudSQL을 사용하는 것은 아무래도 요금적으로 부담이된다.

본 글은 MySQL 도커 이미지파일을 GCE 인스턴스로 배포해서 가볍게 사용해보는 예제를 다루고 있다.

## GCP에서 Mysql 도커 이미지를 띄워보자

그리고 인스턴스를 하나 만든다 사양은 DB 하나 동작할거라 가벼운 걸로 만들었다.

OS는 서버 OS의 **국룰**인 CentOS 7로 설치했다. ( CentOS가 이제 지원 중단된다는 슬픈 얘기를 들었는데 가슴이 아프다. )

<img width="500" alt="스크린샷 2020-12-21 오전 12 21 38" src="https://user-images.githubusercontent.com/43809168/102716956-7ec94200-4322-11eb-850d-8a721497b2f4.png">

그리고 SSH 접근으로 해당 VM 인스턴스에 연결하자.

연결 후에는 yum으로 도커를 설치해주자.

```bash
sudo yum install docker
```

docker ps 명령을 입력해보면 다음과 같은 에러가 발생할 것이다.

이 에러는 도커 데몬이 실행중이지 않아서 발생하는 에러이다.

<img width="784" alt="스크린샷 2020-12-21 오전 12 25 56" src="https://user-images.githubusercontent.com/43809168/102717045-19c21c00-4323-11eb-976c-92e891bc5944.png">

다음 명령어로 도커 데몬을 실행해주자.

```bash
sudo systemctl start docker # 도커 데몬 실행
sudo systemctl enable docker # 인스턴스 부팅 시 자동 실행
```

```bash
docker pull mysql
```

도커 허브로부터 mysql 이미지를 땡겨오자.

<img width="717" alt="스크린샷 2020-12-21 오전 12 29 57" src="https://user-images.githubusercontent.com/43809168/102717134-a8369d80-4323-11eb-923d-fe4f015b16bd.png">

그리고 나서 임시 IP를 고정 IP로 바꿔주자.

GCP 내부에서 같은 프로젝트라고 하더라도 VM 끼리 통신시에는 External IP를 사용해 통신을 한다고 한다.

이 경우에도 외부 인터넷이 아닌 구글 클라우드의 내부 광케이블 네트워크를 이용해 통신하므로 속도, 보안, 비용적 부분에서 상당 수 이점을 얻을 수 있다고 한다.

## Reference

<https://velog.io/@pop8682/Docker-Cannot-connect-to-the-Docker-daemon-at-unixvarrundocker.sock.-Is-the-docker-daemon-running-%EC%97%90%EB%9F%AC-%ED%95%B4%EA%B2%B0>

<https://whitechoi.tistory.com/23>
