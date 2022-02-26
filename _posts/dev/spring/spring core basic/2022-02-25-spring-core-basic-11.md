---
layout: post
date: 2022-02-25 19:00:00
title: "컴포넌트 스캔"
description: "스프링 핵심 원리 - 기본편"
subject: Spring
category: [spring]
tags: [spring, basic, oop]
use_math: true
comments: true
---

[스프링 핵심 원리 - 기본편](https://inf.run/bMZx)을 공부하고 정리하는 포스트입니다.

---

# 컴포넌트 스캔

컴포넌트 스캔이랑 의존관계 자동 주입은 서로 연관 관계가 있습니다. 이 둘의 연관관계를 먼저 알아보겠습니다.

## 컴포넌트 스캔과 의존관계 자동 주입 시작하기

지금까지 스프링 빈을 등록할 때는 자바 코드의 `@Bean`이나 XML의 `<bean>` 등을 통해서 설정 정보에 직접 등록할 스프링 빈을 나열했습니다.  
간단한 예제에서는 개수가 적어서 괜찮았지만, 실무에서는 무수히 많은 스프링 빈을 일일히 등록하기 힘들고, 설정 정보가 커지고, 혹은 누락되는 등의 문제가 생길 수 있습니다.

그래서 스프링은 설정 정보가 없어도 자동으로 스프링 빈을 등록하는 '컴포넌트 스캔'이라는 기능을 제공합니다. 또 의존관계도 자동으로 주입하는 `@Autowired`라는 기능도 제공합니다.

직접 코드를 통해서 알아보기 위해, `AutoAppConfig.java`를 만들어줍니다.

```java
package hello.core;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.FilterType;

@Configuration
@ComponentScan(
    excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class)
)
public class AutoAppConfig {

}
```

`@ComponentScan`은 `@Component` 애노테이션이 붙은 클래스를 찾아서 자동으로 등록해주는데 기존의 직접 등록했던 `AppConfig.class`에 있는 것을 등록하는 것을 방지하기 위해서 필터를 추가했습니다.

컴포넌트 스캔을 사용하기 위해서는 `@ComponentScan`을 붙여주면 됩니다. 수동으로 등록해주던 것과 다르게 `@Bean`으로 등록한 클래스가 없습니다.

컴포넌트 스캔은 이름 그대로 `@Component` 애노테이션이 붙은 클래스를 스캔해서 스프링 빈으로 등록합니다. `@Configuration`의 내부를 살펴보면, `@Component`가 들어있는 것을 확인할 수 있습니다.

사용할 클래스들에 `@Component`를 추가해주겠습니다.

```java
@Component
public class MemoryMemberRepository implements MemberRepository {

    ...

}
```

```java
@Component
public class RateDiscountPolicy implements DiscountPolicy {

    ...

}
```

```java
@Component
public class MemberServiceImpl implements MemberService {

    ...

}
```

여기서 끝나는게 아니라 한 가지 더 해주어야 합니다.  
`@Component`는 직접 등록하는게 아니라 자동으로 등록해주는 것입니다. 코드를 살펴보니 어라? 그러면 의존 관계 주입은 어떻게 할까요?

기존의 AppConfig는 의존관계 주입을 명시해놓았습니다. 하지만 AutoAppConfig는 아무것도 없습니다. 그냥 클래스가 스프링 빈으로 등록됩니다.

그래서 사용하는 것이 `@Autowired`라는 것입니다. `@Autowired`를 생성자에 붙여주면 스프링이 타입에 맞는 것을 찾아와서 의존관계 주입을 자동으로 연결해줍니다.

`@ComponentScan`을 쓰면 빈이 자동으로 등록되지만 의존관계를 설정할 수 있는 방법이 없기 때문에 `@Autowired`를 쓰게됩니다.

```java
@Component
public class MemberServiceImpl implements MemberService {

    private final MemberRepository memberRepository;

    @Autowired // ac.getBean(MemberRepository.class)
    public MemberServiceImpl(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }

    ...

}
```

OrderServiceImpl에도 추가해주겠습니다.

```java
@Component
public class OrderServiceImpl implements OrderService{

    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    @Autowired
    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }

    ...

}
```

이제 테스트 코드를 작성해서 확인해보겠습니다.

```java
public class AutoAppConfigTest {

  @Test
  void basicScan() {
    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AutoAppConfig.class);

    MemberService memberService = ac.getBean(MemberService.class);
    assertThat(memberService).isInstanceOf(MemberService.class);
  }

}
```

기존에는 'AppConfig.class'를 사용했지만 이제는 'AutoAppConfig.class'를 사용합니다. `getBean()`을 사용해서 잘 조회되는지 확인해보면 정상적으로 동작하는 것을 볼 수 있습니다.

`AnnotationConfigApplicationContext`을 사용하는 것은 동일하지만 설정 정보를 다른 클래스로 넘겨줍니다.  
로그를 잘 읽어보면 컴포넌트 스캔이 동작하는 것을 볼 수 있습니다.

<img src="/assets/img/springcore/core63.png" width="50%" align="center"><br/>

### ComponentScan

<img src="/assets/img/springcore/core64.png" width="50%" align="center"><br/>

`@ComponentScan`은 `@Component`가 붙은 모든 클래스를 스프링 빈으로 컨테이너에 등록합니다. 이 때 빈 이름을 주의해야 합니다. 스프링 빈의 기본 이름은 클래스명을 사용하되 맨 앞글자만 소문자를 사용합니다.

예를 들어 'MemberServiceImpl' 클래스는 'memberServiceImpl'과 같은 형식으로 작성해야 합니다. 빈의 이름을 직접 지정하고 싶으면 `@Component("memberService2")`와 같이 할 수 있습니다.

### Autowired 의존관계 자동 주입

<img src="/assets/img/springcore/core65.png" width="50%" align="center"><br/>

생성자에 `@Autowired`를 지정하면 스프링 컨테이너가 자동으로 타입이 같은 빈을 찾아서 주입해줍니다.

<img src="/assets/img/springcore/core66.png" width="50%" align="center"><br/>

생성자에 파라미터가 많아도 다 찾아서 주입해줍니다.

