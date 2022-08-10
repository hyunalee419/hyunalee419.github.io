---
layout: post
title: 의존관계 주입의 종류
categories: [Spring]
tags: [Spring, Java, DI, DependencyInjection]
comments: true
---

## 다양한 의존관계 주입
- 생성자 주입
- 수정자 주입 (setter 주입)
- 필드 주입
- 일반 메서드 주입

### 생성자 주입
- 생성자 호출시점에 딱 1번만 호출되는 것이 보장된다.
- 불변, 필수 의존관계에 사용

```
@Component
public class OrderServiceImpl implements OrderService {
    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;
    
    @Autowired
    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
}
```

- **생성자가 1개만 있으면 @Autowired를 생략해도 자동으로 주입** 된다. 물론 스프링 빈에만 해당된다.

```
@Component
public class OrderServiceImpl implements OrderService {
    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;
    
    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
}
```

### 수정자 주입 (setter 주입)
- 필등의 값을 변경하는 수정자 메서드(setter)를 통해서 의존관계 주입
- **선택, 변경** 가능성이 있는 의존관계에 사용
- [자바빈 프로퍼티 규약](https://greensky0026.tistory.com/245)의 수정자 메서드(setter) 방식을 사용하는 방법

```
@Component
public class OrderServiceImpl implements OrderService {
    private MemberRepository memberRepository;
    private DiscountPolicy discountPolicy;
    
    // @Autowired(required = false)
    @Autowired
    public void setMemberRepository(MemberRepository memberRepository) {
        this.memberRepository = memberRespotiroy;
    }
    
    @Autowired
    public void setDiscountPolicy(DiscountPolicy discountPolicy) {
         
}
```

> `@Autowired`의 기본 동작은 주입할 대상이 없으면 오류가 발생한다. 주입할 대상이 없어도 동작하게 하려면 `@Autowired(required = false)`로 지정하면 된다. (선택적 의존관계)

> 인스턴스를 변경하는 경우가 있을때 수정자 주입 사용 (변경가능성 있는 의존관계)

### 필드 주입

```
@Component
public class OrderServiceImpl implements OrderService {
    @Autowired private MemberRepository memberRepository;
    @Autowired private DiscountPolicy discountPolicy;
}
```

- 필드에 바로 주입하는 방법
- 코드가 간결하지만 외부에서 변경이 불가능하다. (테스트 코드 작성시 임시로 변경하고 할 때 불가능)
- DI 프레임워크가 없으면 아무것도 할 수 없다.
- 사용하지 않는 것이 좋지만 테스트 코드나 @Configuration 같은 곳에서만 특별한 용도로 사용

```
@Test
void fieldInjectionTest() {
    OrderServiceImpl orderService = new OrderServiceImpl();
    
    // orderService.setMemberRepository(new MemberRepository());
    // orderService.setDiscountPolicy(new DiscountPolicy());
    
    orderService.create(1L, "itemA", 10000);
}
```

### 일반 메서드 주입
- 일반 메서드에 `@Autowired`를 사용하는 방식.
- 한번에 여러 필드를 주입 받을 수 있다.
- 일반적으로 생성자, 수정자 주입을 이용하기때문에 잘 사용하지 않는다.

```
@Component
public class OrderServiceImpl implements OrderService {
    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;
    
    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
    
    @Autowired
    public void init(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
}
```

> 스프링 컨테이너가 관리하는 스프링 빈(OrderServiceImpl)이어야 동작한다.

---
출처
- [inflern 스프링 핵심 원리 기본편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8)
출처
