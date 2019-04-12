---
layout: post
title: Python Performance Tips
categories: [Python]
tags: [Python, performance]
comments: true
---

> 해당 페이지는 [Python Performance Tips: Part 1](https://www.monitis.com/blog/python-performance-tips-part-1/)을 번역한 페이지이며, 의역 및 오역이 있을 수 있습니다.

## 기본 제공 함수를 사용하라
Python에서 효율적인 코드를 짤 수 있지만 (C 처럼)기본 제공 함수를 골라쓰기란 어렵다. 아래 함수를 확인하라. 이것들이 매우 빠르다.
<img src="http://www.monitis.com/blog/wp-content/uploads/2012/02/nginxproxy3.png" />

## 긴 문자열인 경우 join() 을 사용하라
`+` 를 사용하여 여러 문자열을 합칠 수 있다. 하지만 Python에서 문자열은 '불변'이다. 모든 `+` 명령에 따라 새로운 문자열을 생성하고 오래된 컨텐츠를 복사한다.
Python의 `array`를 사용하여 개별 문자를 수정하는게 좋다. `join()` 함수를 사용하면 마지막 문자열만 재생성한다.

- 아래 코드가 긴 문자열을 합치는 좋은 방법이다.
```
for chunk in input():
    my_string.join(chunk)
```

## 변수 swap 에 multiple assignment를 사용하라
- 좋은 방법
```
x, y = y, x
```
- 나쁜 방법
```
temp = x
x = y
y = temp
```

## 가능하면 지역 변수를 사용하라
`Python`은 글로벌 변수보다 지역 변수를 검색하는게 더 빠르다. `global` 키워드를 피하라!

## 가능하면 **`in`** 을 사용하라
자료구조를 검색할 때 `in`을 사용하라. 훨씬 빠르고 코드가 깔끔하다.
```
for key in sequence:
    print "found"
```

## 가능하면 import는 필요한 함수에서 사용하라
`import`는 필요한 함수 내에서 작성하는게 좋다. 즉, 일부 모듈이 바로 필요한게 아니라면 나중에 import 해라.
예를 들어, 시작할 때 긴 모듈 목록을 가져오지 않는게 프로그램 속도를 빠르게 할 수 있다. 이 방식이 모듈의 전반적인 성능을 향상시키진 않지만 모듈 로딩 속도를 균등하게 배분할 수 있다.

## 무한루프를 위해 `while 1`을 사용하라
가끔 프로그램에 무한루프가 필요한 경우가 있다. (예를 들어, listening socket) `while True`가 동일한 작업을 수행하지만 `while 1`이 단일 점프 작업(single jump operation)이다. 이 트릭을 Python code에 사용하라.
- 좋은 방법
```
while 1:
    # do stuff, faster with while 1
```
- 나쁜(?) 방법:
```
while True:
    # do stuff, slower with while True
```
> 이거는 Python2에만 해당되는 것 같다. [while (1) Vs. for while(True) — Why is there a difference?](https://stackoverflow.com/a/26004170)을 참고하길 바란다.


...
작성중
