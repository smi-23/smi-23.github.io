---
title: "[Interview] Priority Queue"
excerpt: ""
categories:
    - Interview
tags:
    - [Blog, Github, Git, Github.io, Interview, Data Structure, Priority Queue]
toc : true
toc_sticky: true
date: 2024-01-10
last_modified_at: 2024-01-10
---
# 우선순위 큐

## 우선순위 큐(Priority Queue)란?

- 큐는 먼저 들어간 데이터가 먼저 나오는 선입선출의 특징을 가짐
- 우선순위 큐는 들어간 순서에 상관 없이 `우선순위`가 높은 데이터가 먼저 나온다는 특징을 가짐
- 큐 = 줄서기, 우선순위 큐 = 응급실(들어오는 순서와 나가는 순서가 상관이 없음)
- 우선순위 큐는 `Heap`을 이용해서 구현하는 것이 가장 효율적
    - 배열을 통해 완전 이진 트리를 구현해서 힙을 구현함
    - 배열로 우선순위 큐를 구현하게 되면 삭제 연산 시에 모든 원소를 탐색해서 우선순위를 찾아 비효율적
    - 연결 리스트로 우선순위 큐를 구현하면 접근 시간이 오래걸리고 메모리 오버헤드가 일어날 수 있음
- 대부분 힙을 통해 구현하기 때문에 힙의 특성을 가지고 있음
    - 삽입, 삭제, 탐색

### 우선순위 큐의 활용

- 운영체제의 작업 스케줄링
- Heap Sort (힙 정렬)
- 허프만 코딩
- 다익스트라 알고리즘: 선형 탐색을 이용하는 기존 방식에서 Min Heap을 이용하면 시간복잡도를 O(V^2)에서 O(ElogV)까지 줄일 수 있음(V: 정점의 개수, E: 간선의 개수)