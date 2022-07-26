---
layout: post
title: 컴포넌트 스캔과 의존관계 자동 주입
categories: [Spring]
tags: [Spring, ComponentScan, Java]
comments: true
---

> 지금까지 스프링 빈을 등록할 때는 자바 코드의 @Bean이나 XML의 <bean\> 등을 통해서 직접 스프링 빈을 설정해줬다.
> 빈 등록을 빼먹거나 반복 작업이라는 단점이 존재
  
## Component scan 이란?
`@Bean` `<bean>` 등의 설정 정보가 없어도 스프링이 자동으로 스프링 빈을 등록해주는 기능

### 사용 방법
```
@Configuration
@ComponentScan
public class AppConfig {
}
```
`@ComponentScan`을 사용하면 되며, 이 컴포넌트 스캔 어노테이션은 `@Component` 가 붙은 class 찾아서 자동으로 스프링 빈으로 등록해준다.

```
@Component
public class MemberRespository {
}
```

### 대상 제외
```
@Configuration
@ComponentScan(
	excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class)
)
public class AppConfig {
}
```
`excludeFilters`는 컴포넌트 스캔이 될 때 제외시킬 목록을 말한다.
`@Configuration` 소스코드를 열어보면 `@Component` 어노테이션을 갖고 있기 때문에 컴포넌트 스캔 대상에 포함된다.
컴포넌트 스캔을 사용하면 `@Configuration`이 붙은 설정 정보도 자동으로 등록되기 때문에, AppConfig, TestConfig 등 설정정보도 함계 등록, 실행된다. 그래서 `excludeFilters`를 이용해서 설정정보는 컴포넌트 스캔 대상에서 제외시킨다.


### @Controller, @Service, @Repository
![Component](https://user-images.githubusercontent.com/8050224/180900036-8709f3f7-1e64-4b8c-ab27-7ac68aef5dfe.png)

springframework 에서 제공해주는 `@Contoller`, `@Service`, `@Repository` 는 코드 내부에 `@Component`를 가지고 있다.
따라서 각 코드를 작성할 때 별도로 `@Component`를 붙여주지 않아도 자동으로 컴포넌트 스캔 대상에 포함되기 때문에 스프링 빈으로 등록된다.
```
@Service
public class MemberServiceImpl {
}
```
```
@Repository
public class MemberRepository {
}
```
```
@Contoller
public class MemberController {
}
```

![Rest Controller](https://user-images.githubusercontent.com/8050224/180900091-41491898-4d87-4149-8581-eeca303c9b53.png)


`@Contoller`외에 REST API 를 만들기 위해 자주 사용하는 `@RestContoller`안에는 `@Contoller`를 포함하기 때문에 이것 또한 컴포넌트 스캔 대상에 포함된다.
```
@RestContoller
public class MemberContoller {
}
```

### 동작 방식
`@ComponentScan`은 `@Component`가 붙은 모든 클래스를 스프링 빈으로 등록한다.
이때 스프링 빈의 기본 이름은 클래스명을 사용하되 맨 앞글자만 소문자를 사용한다.
> 기본: MemberServiceImpl 클래스 -> memberServiceImpl
> 지정: `@Component("memberService")` 방식으로 직접 이름 지정이 가능


## 의존관계 자동 주입
기존에는 @Bean을 통해 의존관계 주입을 직접 설정해줬지만 `@Autowired`를 사용하면 스프링 컨테이너가 스프링 빈을 자동으로 주입해준다.
```
@Service
public class MemberServiceImpl impliments MemberService {
	private final MemberRepository memberRepository;
    
    @Autowired
    public MemberServiceImpl(MemberRepository memberRepository) {
    	this.memberRepository = memberRespository;
    }
}
```

> 의존관계 주입 방식에는 여러가지가 있는데 그 중 생성자 주입을 추천한다.
