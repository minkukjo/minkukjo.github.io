---
layout: post
title: Spring Batch의 트랜잭션 범위는 어디까지일까?
subtitle: ''
categories: framework
tags: spring
comments: false
---

## Spring Batch의 트랜잭션 범위는 어디까지일까?

## 개요

어제 팀원들과 이야기 하던 중, 스프링 배치의 트랜잭션에 대해서 이야기가 나와서 오프라인에서 간략하게 설명을 드렸었는데요.

이야기 하고 보니, 팀 내에 공유해도 좋을 만한 내용이라 생각되어서 작성하게 되었습니다.

본 글은 스프링 배치를 쓰면서 스프링 배치의 트랜잭션이 정확히 어떻게 적용되는지를 이해하는 것을 목표로 합니다.

## Chunk와 Task

스프링 배치에서 가장 중요한 개념은 바로 `Task` 와 `Chunk` 입니다.

스프링 배치는 기본적으로 `Task Based`와 `Chunk Based` `Transaction` 두 가지 방식을 지원합니다.

## Task Based

<img width="621" alt="스크린샷 2024-02-10 오후 10 33 10" src="https://github.com/minkukjo/minkukjo.github.io/assets/43809168/6a3fc15d-ab85-48ad-9301-69759f1e718e">

`Task Based` `Transaction`의 경우 단일 작업으로 처리되는 경우 (단순히 동작하는 1회성 배치?)에 적합한 방식입니다.

## Chunk Based

<img width="619" alt="스크린샷 2024-02-10 오후 10 33 32" src="https://github.com/minkukjo/minkukjo.github.io/assets/43809168/d8904408-a7e5-4e74-a4a4-251b714dd2c1">

`Chunk Based`는 우리가 익히 사용해오고 있던 예약의 배치 프로세스입니다.

`Reader` -> `Processor` -> `Writer`로 이어지는 3단 콤보죠.

여기서 이름 그대로 저희는 `Chunk`라고 하는 사이즈를 지정해서 배치 애플리케이션을 작성해왔는데요.

위 스탭만 보면 마치 `Reader 스탭도 트랜잭션으로 엮여있는 것 처럼` 보여서 많이들 헷갈리셨던 것 같습니다.

`Reader`가 트랜잭션으로 엮인 것은 사실 개념적으로는 맞지만, 개발 구현 측면에선 아닙니다.

`Chunk Based` 방식의 철학은 `Chunk 단위로 트랜잭션을 걸것이며, 롤백도 Chunk 단위로 하겠다`라는 것입니다.

아래에서도 자세히 들어가보겠지만, `Chunk Based`는 Reader 함수에서 조회된 결과를 `Chunk` 단위로 자르고, `Processor` 스텝으로 넘기게 되는데, 이 때 넘어가는 `Chunk` 데이터가 트랜잭션으로 묶이는 것이지 `Reader` 스탭 자체가 트랜잭션으로 묶이는 것은 아닙니다.

말이 좀 어려워서 그렇지 막상 코드로 보면 별 것 아닙니다.

## Spring Batch Chunk Based 동작 방식 Deep Dive

<img width="622" alt="스크린샷 2024-02-10 오후 10 34 41" src="https://github.com/minkukjo/minkukjo.github.io/assets/43809168/624a7d45-7ec7-4edd-9664-309a597b2217">

시작은 `TransactionTemplate`에서 시작합니다.

<img width="635" alt="스크린샷 2024-02-10 오후 10 35 29" src="https://github.com/minkukjo/minkukjo.github.io/assets/43809168/5f29e476-9968-42ec-ba1d-cdbb7db49ad9">

본 내용의 핵심인 구현체 `ChunkTransactionCallback`로 진입합니다.

<img width="634" alt="스크린샷 2024-02-10 오후 10 35 51" src="https://github.com/minkukjo/minkukjo.github.io/assets/43809168/21d970a0-8d15-4b80-aadc-92b382e71160">

`doInTransaction` 메소드 내부에서 `tasklet.execute(contribution, chunkContext);`가 있는데, 여기서 tasklet의 구현체 중 `ChunkOrientedTasklet`을 선택합니다. (이유는 예약에서는 Chunk 기반 배치를 사용하기 때문)

<img width="628" alt="스크린샷 2024-02-10 오후 10 36 11" src="https://github.com/minkukjo/minkukjo.github.io/assets/43809168/45dd70fd-37b9-4397-b0f8-681a297ad068">

이 때 눈 여겨서 봐야할 부분은 `chunkProvider.provide(contribution);`와 `chunkProcessor.process(contribution, inputs);` 입니다.

<img width="625" alt="스크린샷 2024-02-10 오후 10 36 32" src="https://github.com/minkukjo/minkukjo.github.io/assets/43809168/0248038f-6ba8-43cc-82b2-3348575d3a7e">

`chunkProvider.provide(contribution);`의 경우 `Reader` 스탭에서 반환된 결과 값을 `Chunk` 단위로 잘라서 저장하는 함수입니다.

잘 보면 리스트에 하나씩 값을 담는 것을 볼 수 있습니다.

<img width="629" alt="스크린샷 2024-02-10 오후 10 36 52" src="https://github.com/minkukjo/minkukjo.github.io/assets/43809168/c893a020-ca2e-475c-8709-ead02c7cc8d5">

위에서 얻어진 `Chunk`를 `process`라는 스탭으로 넘기게 되는데요.

이 때 이 `process`가 우리가 익히 잘 알고 있는 `processor` 스탭 + `writer` 스탭 입니다.

<img width="622" alt="스크린샷 2024-02-10 오후 10 37 09" src="https://github.com/minkukjo/minkukjo.github.io/assets/43809168/89e03bef-de37-41eb-b0de-b80c76695814">

이 프로세서에서 `transform`이라는 메소드가 우리가 작성해둔 `processor` 로직을 기반으로 객체의 타입을 변경하는 부분입니다.

거기서 얻어진 `Chunk`를 `writer`로 보내게 됩니다.

여기 까지 보면, 결국 트랜잭션은 `Chunk`를 `reader`가 반환한 시점부터 적용된다고 볼 수 있는데요.

실제로 확인해본 결과 스프링 배치는 `reader` 함수에서는 별도로 트랜잭션을 걸어주지 않는다는 사실을 확인할 수 있었습니다.

## 출처

-  스프링 공식 문서
-  나의 뇌와 눈
