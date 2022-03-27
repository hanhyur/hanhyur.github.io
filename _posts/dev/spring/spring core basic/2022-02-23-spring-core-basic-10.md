---
layout: post
date: 2022-02-23 23:00:00
title: "싱글톤 컨테이너 2부"
description: "스프링 핵심 원리 - 기본편"
subject: Spring
category: [ spring boot basic ]
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

스프링 컨테이너는 싱글톤 레지스트리입니다. 따라서 스프링 빈이 싱글톤이 되도록 보장해주어야 합니다. 하지만 자바코드까지 어떻게 하기는 어렵습니다.

그래서 스프링은 클래스의 바이트코드를 조작하는 라이브러리를 사용합니다. 이에 대한 비밀은 바로 `@Configuration`을 적용한 `AppConfig`에 있습니다.

```java
  @Test
  void configurationDeep() {
    ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
    AppConfig bean = ac.getBean(AppConfig.class);

    System.out.println("bean = " + bean.getClass());
  }
```

<img src="/assets/img/springcore/core58.png" width="50%" align="center"><br/>

순수한 클래스라면 `class hello.core.AppConfig`가 출력이 되어야 합니다. 그런데 예상과 달리 클래스 명 뒤에 `CGLIB`이 붙으면서 뭔가 복잡해진 것을 볼 수 있습니다.

이것은 스프링이 <b>CGLIB</b>이라는 바이크토드 조작 라이브러리를 사용해서 `AppConfig` 클래스를 상속받은 임의의 다른 클래스를 만들고, 그것을 스프링 빈에 등록한 것입니다.

<img src="/assets/img/springcore/core59.png" width="50%" align="center"><br/>

이 임의의 다른 클래스가 싱글톤이 보장되도록 해줍니다. 실제로 CGLIB의 내부 코드는 매우 복잡하지만 다음과 같이 동작할 것이라고 예상해 볼 수 있습니다.

```java
@Bean
public MemberRepository memberRepository() {

  if (memoryMemberRepository가 이미 스프링 컨테이너에 등록되어 있으면?) {
    return 스프링 컨테이너에서 찾아서 반환;
  } else { //스프링 컨테이너에 없으면
    기존 로직을 호출해서 MemoryMemberRepository를 생성하고 스프링 컨테이너에 등록
    return 반환
  }

}
```

@Bean이 붙은 메서드마다 이미 스프링 빈이 존재하면(스프링 컨테이너에 등록되어 있으면) 존재하는 빈을 반환하고, 스프링 빈이 없으면 생성해서 스프링 빈으로 등록하고 반환하는 코드가 동적으로 만들어진 덕분에 싱글톤이 보장되는 것입니다.

### 만약 @Configuration을 적용하지 않고, @Bean만 적용하면 어떻게 될까요?

`@Configuration`을 붙이면 바이트코드를 조작하는 CGLIB 기술을 사용해서 싱글톤을 보장해줍니다. 하지만 `@Bean`만 적용하면?

```java
// @Configuration
public class AppConfig {
  ...
}
```

<img src="/assets/img/springcore/core60.png" width="50%" align="center"><br/>

CGLIB 기술 없이 순수한 AppConfig로 스프링 빈에 등록된 것을 확인할 수 있습니다.

<img src="/assets/img/springcore/core61.png" width="50%" align="center"><br/>

그런데 자세히보니 `memberRepository`가 3번 호출된 것을 볼 수 있습니다. 다른 싱글톤이 깨진 것입니다. 첫 번째는 @Bean에 의해 스프링 컨테이너에 등록하기 위해서 호출한 것이고, 나머지 2번은 각각 new로 인해 호출되면서 발생한 것입니다.

싱글톤이 깨졌기 때문에 당연히 각각 다른 인스턴스를 가진 것을 볼 수 있습니다.

<img src="/assets/img/springcore/core62.png" width="50%" align="center"><br/>

그리고 이 `memberRepository`들은 스프링 빈이 아닙니다. 직접 `new MemoryMemberRepository`로 넣어준 것과 같습니다. 스프링 컨테이너 관리하지 않습니다.

정리하면 @Bean만 사용해도 스프링 빈으로 등록되지만, 싱글톤을 보장하지 않습니다. `memberRepository()`처럼 의존관계 주입이 필요해서 메서드를 직접 호출할 때 싱글톤을 보장하지 않습니다. 따라서 스프링 설정 정보는 항상 `@Configuration`을 사용해야 합니다.