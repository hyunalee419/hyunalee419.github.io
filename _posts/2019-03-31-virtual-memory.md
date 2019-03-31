---
layout: post
title: 가상 메모리 영역
categories: [memory]
tags: [memory]
comments: true
---
> 이 페이지는 C언어를 기준으로 가상메모리 영역에 대해 설명하고 있다.

# 메모리

-   데이터와 명령어를 저장하는 곳
-   컴퓨터가 작업을 할 때 그 작업을 어떻게 하는지 참고하기위한 자료가 메모리에 있다.
-   메모리는 한정되어 있으므로 잘 사용해야 한다.

# 가상메모리

32비트 시스템에서 프로세스(실행중인 프로그램) 생성시 4GB의 메모리를 할당받을 수 있는데, 이는 RAM 만으로는 충당하기에 부족하다. 그래서 OS는 RAM과 하드디스크를 하나로 묶어 가상메모리로 관리한다.

대부분의 시스템에서는 주로 페이징(paging)이라는 기법으로 가상메모리를 관리한다.

가상메모리 영역은 code, data(data, BSS), heap, stack 영역으로 나뉜다.

## code 영역 (text)

-   코드 자체를 구성하는 메모리 영역으로 Hex 파일이나 BIN 파일 메모리다.
-   프로그램 명령이 위치하는 곳으로 기계어로 제어되는 메모리 영역이다.

<pre>
int a; 를 선언 후 실행
컴파일시 생성되는 기계어가 code 영역에 들어가는데, 위 예시에서는 "4byte 공간을 stack에 생성하라" 라는 명령이 code 영역에 들어간다.
</pre>

## data 영역

-   전역변수(global), 정적변수(static), 배열(array), 구조체(structure) 등이 저장된다.
-   data 영역은 "data 영역", "BSS 영역"으로 나뉜다.
    -   data 영역: 초기화된 데이터가 저장됨
    -   BSS(Block Stated Symbol) 영역: 초기화되지 않은 데이터가 저장됨
-   프로그램이 실행될 때 생성되고, 프로그램이 종료되면 시스템에 반환된다.
-   함수 내부에 선언된 static 변수는 프로그램이 실행될 때 공간만 할당되고, 그 함수가 실행될 때 초기화 된다.

## heap 영역

-   메모리를 동적으로 할당하고자 할 때 위치하는 메모리 영역으로, 동적 데이터 영역이라고 부르며 메모리 주소 값에 의해서만 참조되고 사용되는 영역이다.
-   이 영역에 데이터를 저장하기 위해서 C는 malloc(), C++은 new() 함수를 사용한다.

## static 영역

-   프로그램이 자동으로 사용하는 임시 메모리 영역이다.
-   지역(local)변수, 매개변수(parameter), 리턴 값 등 잠시 사용 되었다가 사라지는 데이터를 저장하는 영역이다.
-   함수 호출시 생성되고, 함수가 끝나면 시스템에 반환된다.
-   스택 사이즈는 각 프로세스 마다 할당되지만 프로세스가 메모리에 로드될 때 스택 사이즈가 고정되어 있어 런타임시에 스택 사이즈를 바꿀 수는 없다.
-   명령 실행시 자동 증가/감소하기 때문에 보통 메모리의 마지막 번지를 지정한다.

## 메모리 할당 영역

<img src="{{ "/assets/virtual-memory.png" | absolute_url }}" alt="drawing" style="width:500px;"/>

code(text), data, stack 영역은 컴파일러가 알아서 메모리 영역을 결정한다. 즉, 컴파일할 때 data 영역과 stack 영역의 크기를 계산해서 필요한 메모리 공간을 가지게 된다. heap 영역은 개발자에 의해 프로그램 동작시 결정된다.

code, data, heap 영역은 하위 메모리부터 할당되고, stack 영역은 상위 메모리부터 할당된다.

# SMA (Static Memory Allocation)

-   정적 메모리; 메모리의 data, stack 영역을 사용
-   data 영역: 프로그램 시작과 동시에 할당된 영역이 잡히고 끝나면 OS에 반환
-   stack 영역: 함수 시작과 동시에 할당된 영역이 잡히고 끝나면 OS에 반환

# DMA (Dynamic Memory Allocation)

-   동적 메모리; 메모리의 heap 영역을 사용
-   heap 영역: stack에서 pointer 변수를 할당하고, 그 pointer가 가리키는 heap 영역의 임의의 공간부터 원하는 크기만큼 할당해 사용한다.

# HEAP overflow, STACK overflow

-   HEAP overflow: heap이 아래서부터 위로 주소값을 채우다가 stack 영역을 침범하는 경우
-   STACK overflow: stack이 heap 영역을 침범하는 경우
