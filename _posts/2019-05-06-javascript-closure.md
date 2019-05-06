---
layout: post
title: JavaScript Closure
categories: [JavaScript]
tags: [JavaScript, Closure]
comments: true
---
# Closure (클로저)
클로저는 외부함수의 변수에 접근할 수 있는 내부 함수를 말한다.

아래 예제는 클로저를 설명하는 블로그에서 가장 많이 보는 것일 것이다.
```
for (var i = 0; i < 10; i++) {

}
```

클로저는 외부함수(포함하고 있는)의 변수에 접근할 수 있는 내부 함수를 일컫습니다. 스코프 체인(scope chain)으로 표현되기도 합니다. 클로저는 세가지 스코프 체인을 가집니다: 클로저 자신에 대한 접근(자신의 블럭내에 정의된 변수), 외부 함수의 변수에 대한 접근, 그리고 전역 변수에 대한 접근. 이렇게 3단계로 구분할 수 있습니다.
