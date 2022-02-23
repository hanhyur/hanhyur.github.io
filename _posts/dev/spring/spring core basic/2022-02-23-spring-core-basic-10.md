---
layout: post
date: 2022-02-23 23:00:00
title: "싱글톤 컨테이너 2부"
description: "스프링 핵심 원리 - 기본편"
subject: Spring
category: [ spring ]
tags: [ spring, basic, oop ]
use_math: true
comments: true
---

[스프링 핵심 원리 - 기본편](https://inf.run/bMZx)을 공부하고 정리하는 포스트입니다.

---

## @Configuration과 싱글톤

`@Configuration`의 비밀에 대해서 하나씩 알아보겠습니다. `@Configuration`은 사실 싱글톤을 위해서 존재하는 것입니다.

AppConfig 코드를 보겠습니다.

```java
@Configuration
public class AppConfig {

  @Bean
  public MemberService memberService() {
    return new MemberServiceImpl(memberRepository());
  }

  @Bean
  public MemberRepository memberRepository() {
    return new MemoryMemberRepository();
  }

  @Bean
  public OrderService orderService() {
    return new OrderServiceImpl(memberRepository(), discountPolicy());
  }

  @Bean
  public DiscountPolicy discountPolicy() {
//    return new FixDiscountPolicy();
    return new RateDiscountPolicy();
  }

}
```

여기서 `return new MemberServiceImpl(memberRepository());`를 보면, 스프링 빈이 memberService를 생성할 때 호출하는데 당연히 자바 코드이니 `MemberServiceImpl`이 호출되고 `memberRepository`도 호출될 것입니다. 그러면 결과적으로 `new MemoryMemberRepository()`가 생성됩니다.

즉, `@Bean memberService -> new MemoryMemberRepository()`와 같은 흐름이 됩니다. `orderService`도 마찬가지로 `@Bean orderService -> new MemoryMemberRepository()`가 호출이 되는데, '어? 이러면 싱글톤이 깨지는게 아닌가'라고 고민하게 됩니다.

바로 테스트코드를 돌려서 확인해 보겠습니다.

```java
public class ConfigurationSingletonTest {

  @Test
  void configurationTest() {
    ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

    MemberServiceImpl memberService = ac.getBean("memberService", MemberServiceImpl.class);
    OrderServiceImpl orderService = ac.getBean("orderService", OrderServiceImpl.class);

    MemberRepository memberRepository = ac.getBean("memberRepository", MemberRepository.class);

    MemberRepository memberRepository1 = memberService.getMemberRepository();
    MemberRepository memberRepository2 = orderService.getMemberRepository();

    System.out.println("memberService -> memberRepository1 = " + memberRepository1);
    System.out.println("orderService -> memberRepository2 = " + memberRepository2);
    System.out.println("memberRepository = " + memberRepository);

    Assertions.assertThat(memberService.getMemberRepository()).isSameAs(memberRepository);
    Assertions.assertThat(orderService.getMemberRepository()).isSameAs(memberRepository);
  }
}
```

<img src="/assets/img/springcore/core56.png" width="50%" align="center"><br/>

실행 결과를 보면 3개 모두 같은 것을 볼 수 있습니다. `new MemoryMemberRepository();`라는 로직이 3번 실행되어서 다른 인스턴스가 생성되어야 하는데 어떻게 된 걸까요?

로그를 남겨서 확인해보겠습니다.

```java
@Configuration
public class AppConfig {

  @Bean
  public MemberService memberService() {
    System.out.println("call AppConfig.memberService");
    return new MemberServiceImpl(memberRepository());
  }

  @Bean
  public MemberRepository memberRepository() {
    System.out.println("call AppConfig.memberRepository");
    return new MemoryMemberRepository();
  }

  @Bean
  public OrderService orderService() {
    System.out.println("call AppConfig.orderService");
    return new OrderServiceImpl(memberRepository(), discountPolicy());
  }

  @Bean
  public DiscountPolicy discountPolicy() {
//    return new FixDiscountPolicy();
    return new RateDiscountPolicy();
  }

}
```

예측한 결과에서는 "call AppConfig.memberRepository"가 3번 나올 것으로 예상됩니다. 결과를 보겠습니다.

<img src="/assets/img/springcore/core57.png" width="50%" align="center"><br/>

예상과 달리 "call AppConfig.memberRepository"는 1번만 호출되었습니다. 도대체 어떤 방법으로 한 것일까요?

---

## @Configuration과 바이트 코드 조작의 마법

