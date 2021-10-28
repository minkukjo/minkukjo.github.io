---
layout: post
title: 백준 1068 트리
subtitle: ''
categories: cs
tags: Algorithm
comments: false
---

## 문제

![스크린샷 2021-10-30 오전 1 59 31](https://user-images.githubusercontent.com/43809168/139474297-10b5069e-6d76-487d-898d-bab3dc331ddc.png)

백만년만에 풀어보는 백준 문제

처음에 단순히 DFS 돌리면 풀리겠거니 했는데 그렇지 않았다.

이 문제의 핵심은 세 가지다.

1. 트리간의 관계를 인접리스트로 표현

2. 인접 리스트를 순회하면서 삭제된 노드의 경우 지나감 ( DFS )

3. DFS 순회 중 자식 노드가 없는 경우 (인접 리스트에서 조회된 노드가 인접리스트에 없는 경우)를 체크해서 카운팅

```java

```