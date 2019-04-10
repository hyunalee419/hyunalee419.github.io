---
layout: post
title: Python Performance Tips
categories: [Python]
tags: [Python, performance]
comments: true
---

> 해당 페이지는 [Python Performance Tips: Part 1](https://www.monitis.com/blog/python-performance-tips-part-1/)을 번역한 페이지이며, 오역이 있을 수 있습니다.

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

...
작성중
