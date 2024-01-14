---
title: "[Interview] Array, ArrayList, Linked List의 차이"
excerpt: ""
categories:
    - Interview
tags:
    - [Blog, Github, Git, Github.io, Interview, Array, List, ArrayList, Linked List]
toc : true
toc_sticky: true
date: 2024-01-12
last_modified_at: 2024-01-12
---
# Array, Arraylist, Linked List차이

## Array

- **크기 고정**: 생성시 크기가 고정, 동적으로 크기를 조절하기 어려움
- **연속적인 메모리 할당**: 원소들은 연속적으로 메모리에 할당되어 있어 인덱스를 통한 빠른 접근 가능
- **크기 변경 *오버헤드**: 크기를 변경하려면 새로운 배열을 할당하고 이전의 원소를 복사해야 함
    - 새로운 배열 할당
    - 기존 원소의 복사
    - 기존 배열의 할당 해제(Java의 경우 Garbage Collection)

****오버헤드**: 어떤 작업을 수행하기 위해 필요 이상으로 들어가는 비용*

### 시간 복잡도

O(1)        

- 인덱스를 통한 탐색

O(n)

- 삽입. 삭제(후 처리로 다른 원소들을 이동시켜야 하기 때문)
- 전체, 특정 값  탐색

## ArrayList

- **가변 크기**: 동적으로 크기를 조절할 수 있는 배열 기반의 자료구조
    - Array의 크기를 조절하는 방식과 같지만 이를 내부적으로 구현해 사용자가 직접 관여할 필요가 없음

### 시간 복잡도

O(1)        

- 인덱스를 통한 탐색

O(n)

- 삽입. 삭제(후 처리로 다른 원소들을 이동시켜야 하기 때문)
    - 기본적으로 배열과 같지만 동적으로 메모리에 새롭게 할당해야 하기 때문에 성능은 더 안 좋을 수 있음
- 전체, 특정 값  탐색

## LInked List

- **가변 크기**: 동적으로 크기 조절 가능
- **불연속적 메모리 할당**: 각 노드가 다음 노드를 가리키는 포인터를 가지므로 불연속적으로 메모리에 할당
    - 따라서 접근속도가 느림
- **데이터 삽입 및 삭제 용이**: 중간에 데이터를 추가하거나 삭제할 때 해당 노드의 포인터만 조절하면 되므로 효율적

### 시간 복잡도

O(1)        

- 처음이나 끝의 삽입, 삭제
- 삽입 및 삭제 그 자체

O(n)

- 탐색
- 특정 위치의 삽입, 삭제