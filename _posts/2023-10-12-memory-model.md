---
title: "[Memory] 메모리 모델"
excerpt: ""
categories:
    - CS
tags:
    - [Blog, CS, Github, Git, Github.io, Memory]
toc : true
toc_sticky: true
date: 2023-10-12
last_modified_at: 2023-10-12
---

# 임시 저장 수정 해야함

![Alt text](/assets/img/2023-10-12-memory/image-memory.png)
![Alt text](/assets/img/2023-10-12-memory/image.png)

프로그램이 실행되기 위해서는 먼저 프로그램이 메모리에 로드(load)되어야 합니다.
또한, 프로그램에서 사용되는 변수들을 저장할 메모리도 필요합니다.
따라서 컴퓨터의 운영체제는 프로그램의 실행을 위해 다양한 메모리 공간을 제공하고 있습니다.
프로그램이 운영체제로부터 할당받는 대표적인 메모리 공간은 4가지 있습니다.

1. 코드(code) 영역

2. 데이터(data) 영역

3. 스택(stack) 영역

4. 힙(heap) 영역

## Code 영역
실행할 프로그램의 코드가 저장됩니다(컴파일러로 번역된 기계어 형태로 저장). CPU는 이 영역에서 명령어를 하나씩 가져와 처리하게 됩니다.

## Data 영역
전역변수와 정적변수가 저장됩니다. 이 변수들은 프로그램이 시작될 때 할당되어 프로그램 종료 시 소멸됩니다.
- 더 자세히 들어가면 BSS(Block Stated Symbol) 영역이 있는데요, 이 영역에는 초기화 되지 않은 전역변수가 저장됩니다. 초기화 된 전역변수는 Data 영역에 저장되어 비휘발성 메모리인 ROM에 저장되는데 이 부분은 비용이 많이 들어 RAM에 저장될 것과 ROM에 저장될 것을 구분하기 위해 영역을 구분해 사용합니다.

## Stack 영역
정적 메모리 할당
함수의 호출과 관계되는 지역 변수와 매개변수가 저장되는 영역
함수의 호출과 함께 할당되며, 함수의 호출이 완료되면 소멸
푸시(push) 동작으로 데이터를 저장하고, 팝(pop) 동작으로 데이터를 인출
후입선출(LIFO, Last-In First-Out) 방식
메모리의 높은 주소에서 낮은 주소의 방향으로 할당
### 장점
- 매우 빠른 액세스(할당,해제가 빠름)
- 변수를 명시적으로 할당 해제 할 필요 X
### 단점
- 메모리 크기 제한
- 지역 변수만
- 스택 영역은 메모리의 높은 주소에서 낮은 주소의 방향으로 할당

## Heap 영역
동적 메모리 할당
사용자가 직접 관리할 수 있는 ‘그리고 해야만 하는’ 메모리 영역
사용자에 의해 메모리 공간이 동적으로 할당되고 해제
메모리의 낮은 주소에서 높은 주소의 방향으로 할당
### 장점
- 변수는 전역적으로 액세스 가능
- 메모리 크기 제한 X
### 단점
- 상대적으로 느린 액세스(할당,해제가 느리다)
- 메모리를 관리 필요(변수를 할당하고 해제하는 책임이 있다)

 **Reference**<br>
<a href="https://velog.io/@bibi6666667/CS05-%EB%A9%94%EB%AA%A8%EB%A6%AC%EA%B5%AC%EC%A1%B0-CPU-%EA%B0%80%EC%83%81-%EB%A9%94%EB%AA%A8%EB%A6%AC">@bibi6666667</a>
<a href="https://zangzangs.tistory.com/107">https://zangzangs.tistory.com/107</a>
<a href="https://velog.io/@cchloe2311/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C-%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EA%B5%AC%EC%A1%B0">https://velog.io/@cchloe2311/</a>