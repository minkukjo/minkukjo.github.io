---
layout: post
title: Spring와 Mysql Docker 연동해보기
subtitle: ''
categories: devops
tags: docker
comments: false
---

## Spring에서 MySql 도커 컨테이너 연동

```bash
docker pull mysql
```

도커 허브로부터 mysql 이미지를 내려받는다.

```bash
docker run -d -p 3307:3306 -e MYSQL_ROOT_PASSWORD=1234 --name test-mysql mysql

docker ps

# 출력

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                               NAMES
72f912ad563b        mysql               "docker-entrypoint.s…"   4 seconds ago       Up 4 seconds        0.0.0.0:3306->3306/tcp, 33060/tcp   test-mysql
```

도커가 잘 실행되고 있다면, 다음 명령어를 입력하여 접속해보자

```bash
docker exec -it test-mysql bash

# 도커 컨테이너에 접속
mysql -u root -p

# 1234를 입력하여 DB에 접속하자.
Enter passowrd :

mysql> create database jpa;
```

컨테이너에 접속해 `jpa`라는 이름의 데이터베이스를 생성 후 `application.properties` 파일을 다음과 같이 셋팅해주자.

(참고로 필자는 .yml을 사용했음)

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3307/jpa?serverTimezone=UTC&characterEncoding=UTF-8
    username: root
    password: 1234
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true
    properties:
      hibernate:
        format_sql: true
    database-platform: org.hibernate.dialect.MySQL5Dialect
```

실행해보면 잘 연결이 되는 것을 확인할 수 있다.
