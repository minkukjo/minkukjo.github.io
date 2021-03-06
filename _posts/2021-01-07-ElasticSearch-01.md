---
layout: post
title: Elasticsearch란?
subtitle: ''
categories: devops
tags: es
comments: false
---

## What is Elasticsearch?

엘라스틱 서치 (이하 ES)는 샤이 배논이 만든 아파치 루씬 기반의 오픈소스 분산 검색엔진이다.

![unnamed](https://user-images.githubusercontent.com/43809168/73746058-baedc580-4797-11ea-8f40-781bbd131f33.jpg)

위 사진은 엘라스틱 서치의 CEO이자 개발자인 샤이 배넌의 사진이다.

~~아마존도 그렇고 왜 CEO급 개발자들은 하나같이...~~

로맨티스트 샤이 배논은 요리학원을 다니는 아내를 위한 요리법 검색엔진을 만들기 위해 시작한 프로젝트는 Compass라는 초기 검색엔진을 완성시켰다.

샤이 배넌은 Compass에 루씬 검색엔진을 적용시키려고 했었고, 루씬이 가진 한계를 보완하기 위해 새로운 검색엔진 프로젝트를 시작했고 이것이 지금의 ES의 기원이다.

샤이는 이 프로젝트를 오픈소스로 공개하였고 수많은 검색개발자들에게 인기를 얻으며 급속도로 성장하였다.

~~아내는 지금도 요리 레시피 검색 엔진을 기다리고 있다고 한다...~~

![image](https://user-images.githubusercontent.com/43809168/73745731-08b5fe00-4797-11ea-8ab2-304b2c1c47fe.png)

ES의 첫 로고는 소나무였다.

로고에서도 느껴지듯이 그는 동양 문화에 관심이 많았다.

샤이 배논은 트위터와 공식 아이디로 kimchy를 사용하고 있으며 엘라스틱 서치 공식 문서의 예제들에도 항상 사람의 이름에 kimchy가 들어가있는 것을 종종 볼 수 있다.

~~주모!~~

그 이유는 그가 과거 한국에서 생활한 적이 있었기 때문에 김치를 좋아하기 때문이라고 언급한적이 있다.

또한 kimchy는 샤이 배논의 어머니의 성씨이기도 하다.

그러면 어떻게 Elasticsearch는 검색엔진 분야에 있어 급격한 성장을 할 수 있었을까?

## ES 특징

### 오픈소스

ES는 오픈소스로 배포된 프로젝트이다. 이는 엘라스틱 서치가 유명해지고 발전할 수 있었던 큰 이유 중 하나이다.

### 분산 시스템, 확장성

루씬을 사용한 검색엔진은 ES뿐만이 아니다. Solr 또한 루씬 기반의 오픈소스 검색엔진이다. 그러나 Solr는 처음부터 분산 시스템을 염두해두고 개발되었기 때문에 주키퍼 같은 별도의 코디네이터가 필요했다.

그러나 ES는 네트워크상에 있는 노드들과 바인딩돼 클러스터를 구성할 수 있다.

샤드를 기반으로 색인 데이터를 관리하고 운영중에도 노드가 추가되면 샤드를 새로운 노드로 적절히 재분배하여 유지하기 때문에 쉽게 스케일 아웃(확장)이 가능하다.

### 고가용성

샤드는 기본적으로 하나 이상의 복제본을 중복되지않는 서로 다른 노드에 배치한다. 따라서 시스템 운영 중 특정 노드가 다운되더라도 데이터 유실 없이 동일한 검색결과를 얻을 수 있기 때문에 높은 가용성을 제공한다.

### 실시간 검색

ES는 실시간 검색 시스템이다. 클러스터가 실행되는 동안 계속해서 데이터가 입력된다. (검색엔진에서는 색인(indexing)이라고 한다.)

그와 동시에 실시간에 가까운 속도로 색인된 데이터의 검색과 분석이 가능하다.

### 전문(Full-Text) 검색 엔진

루씬은 기본적으로 (inverted file index)라는 구조로 데이터를 저장한다.

이는 루씬을 기반으로 만들어진 ES도 동일하다.

이런 특성을 전문 검색이라고 한다.

### JSON 문서 기반

ES는 문서 기반으로 정보를 저장하기 때문에 직관적이다.

ES의 질의에 사용되는 쿼리문과 쿼리에 대한 결과 모두 JSON이기 때문에 사용자 입장에서는 직관적이라는 장점이 있다.

### RESTful API

ES는 Rest API를 기본으로 지원하며 모든 데이터의 조회,삭제,입력,수정을 HTTP 프로토콜 메소드를 통해 처리한다.

## ES와 RDBMS의 비교

<img width="690" alt="스크린샷 2020-02-04 오후 10 06 01" src="https://user-images.githubusercontent.com/43809168/73747320-8cbdb500-479a-11ea-82dd-b90a5c307b47.png">

Type은 7버전부터 없어졌다.

## 루씬 세그먼트

Inverted Index, Doc Value, 원본 문서 등을 저장하고 있는 단위파일이다.

<img width="281" alt="스크린샷 2020-02-04 오후 10 14 42" src="https://user-images.githubusercontent.com/43809168/73747906-c216d280-479b-11ea-9637-c26d997ad0e5.png">

[세그먼트의 내부]

루씬은 inverted index를 하나의 거대한 파일이 아닌 여러개의 작은 파일로 관리한다.

세그먼트는 입력 버퍼가 가득차거나 1초마다 하나씩 생성된다.

**한번 생성된 세그먼트는 변경되지 않는다.**

그래서 Update는 없으며 Indert&Delete이다.

문서를 삭제하면 문서를 삭제했다고만 표시하고 검색에서 제외시킨다.

나중에 세그먼트를 병합하는 과정을 거치는데 병합과정에서 삭제된 문서를 제외하고 나머지 문서들을 모아서 새로운 세그먼트를 만든다.

- 그래서 문서를 삭제해도 세그먼트 병합을 거치기 전까지 스토리지 용량은 줄어들지 않는다.

세그먼트 병합은 비용이 큰 동작이다.

- 디스크 I/O 작업
- 시스템이 느려진다.
- 가능하면 사용자들이 적은 시간에 하는 것이 좋다.

## Reference

마이크로소프트웨어 388호: 검색의 재발견(Rethinking Search)

<https://victorydntmd.tistory.com/308>

<https://www.slideshare.net/kjmorc/ss-80803233>
