---
title: "[Interview] Data Structure"
excerpt: ""
categories:
    - Interview
tags:
    - [Blog, Github, Git, Github.io, Interview, Data Structure, Linear, NoneLinear]
toc : true
toc_sticky: true
date: 2024-01-08
last_modified_at: 2024-01-08
---
# 선형, 비선형 자료구조

## 선형 자료구조(Linear Data Structure)

자료들이 선형(Linear)한 순서로 배치되어 있는 자료 구조

이는 데이터 간의 관계가 1대1로 연결되어 있으며, 각 요소가 바로 앞 또는 바로 뒤의 요소와 직접적으로 연결되어 있는 형태를 의미
![Alt text](/assets/img/2024-01-08-data-structure/image.png)
1. **배열 (Array)**
    - 동일한 자료형의 요소들이 연속적인 메모리 공간에 저장
    - 각 요소는 인덱스를 사용하여 직접적으로 접근 가능
    - 예) **`int[] arr = {1, 2, 3, 4, 5};`**
2. **연결 리스트 (Linked List)**
    - 각 노드가 데이터와 다음 노드를 가리키는 포인터로 이루어진 구조
    - 각 노드는 직전 노드와 직후 노드와 연결
    - 예) **`1 -> 2 -> 3 -> 4 -> 5`**
3. **스택 (Stack)**
    - 데이터를 저장하는 일종의 선형 자료구조로, Last In First Out (LIFO)
    - 데이터의 삽입과 삭제가 한쪽 끝(top)에서 이루어짐
    - 예) **`push(1), push(2), push(3), pop() -> 3, push(4)`**
4. **큐 (Queue)**
    - 데이터를 저장하는 일종의 선형 자료구조로, First In First Out (FIFO)
    - 데이터의 삽입은 한쪽 끝(rear)에서, 삭제는 다른 쪽 끝(front)에서 이루어짐
    - 예) **`enqueue(1), enqueue(2), enqueue(3), dequeue() -> 1, enqueue(4)`**
5. **덱 (Deque, Double-ended Queue)**
    - 양쪽 끝에서 삽입과 삭제가 모두 가능한 자료 구조
    - 큐(Queue)와 스택(Stack)의 특징을 모두 가지고 있음
    - 예) **`addFirst(1), addLast(2), removeFirst() -> 1, addLast(3), removeLast() -> 3`**

## 비선형 자료구조(NoneLinear Data Structure)

데이터 간의 관계가 선형적이지 않은 자료 구조를 의미

비선형 자료구조에서는 각 요소들이 특정한 순서나 계층 구조에 따라 배치 ❌

1. **그래프 (Graph)**
    - 그래프는 정점(Vertex, Node)과 간선(Edge)으로 구성된 자료 구조로, 각 정점은 서로 연결되어 있음
    - 방향 그래프(Directed Graph)와 무방향 그래프(Undirected Graph)로 나뉠 수 있음
    - 그래프는 순환 혹은 비 순환일 수 있음
    - 예) 소셜 네트워크에서 친구 관계를 표현하는 그래프
2. **트리 (Tree)**
    - 트리는 계층적인 구조를 가진 자료 구조로, 하나의 루트 노드에서 시작하여 여러 레벨의 자식 노드로 이어져 있음
    - 각 노드는 부모-자식 관계를 가지며, 루트 노드를 제외한 모든 노드는 부모에 대한 레벨이 존재
    - 이진 트리, 이진 탐색 트리 등 다양한 종류의 트리 존재
    - 예) 파일 시스템의 디렉토리 구조
3. **해시 테이블 (Hash Table)**
    - 해시 테이블은 키-값 쌍을 저장하는 자료 구조로, 각 키는 해시 함수를 통해 특정 위치에 매핑
    - 충돌(Collision)을 방지하기 위한 다양한 기법이 사용
    - 해시 테이블은 효율적인 검색, 삽입, 삭제 연산을 제공
    - 예) 데이터베이스에서 인덱싱에 사용되는 해시 테이블
4. **힙 (Heap)**
    - 힙은 완전 이진 트리의 일종으로, 최솟값이나 최댓값을 빠르게 찾아내기 위해 사용
    - 최소 힙(Min Heap)과 최대 힙(Max Heap)으로 나뉠 수 있음
    - 힙은 주로 우선순위 큐 등에서 사용
    - 예) 운영체제에서의 프로세스 스케줄링
5. **그래프와 트리를 기반으로 하는 다양한 구조**
    - 이외에도 그래프와 트리를 변형한 다양한 자료 구조들이 존재
    - 예) 이진 힙, 트라이(Trie), AVL 트리, B-트리 등