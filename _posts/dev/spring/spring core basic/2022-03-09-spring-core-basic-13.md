---
layout: post
date: 2022-03-09 23:30:00
title: "의존관계 자동 주입 2부"
description: "스프링 핵심 원리 - 기본편"
subject: Spring
category: [spring]
tags: [spring, basic, oop]
use_math: true
comments: true
---

[스프링 핵심 원리 - 기본편](https://inf.run/bMZx)을 공부하고 정리하는 포스트입니다.

---

## 롬복과 최신 트렌드

실제 개발을 해보면, 대부분 불변이고 생성자에 final 키워드를 사용하게 됩니다. 하지만 매번 생성자를 만들고, 주입 받은 값을 대입하는 코드도 만들어야 합니다.  
필드 주입처럼 좀 더 편리하게 사용하는 방법이 없을까요?

다음 코드는 기본 코드입니다.

```java
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

생성자가 1개인 경우, `@Autowired`를 생략할 수 있습니다.

```java
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

아직 코드가 긴 거 같습니다. 여기에 <b>롬복</b> 라이브러리를 적용해보겠습니다. `Lombok`이란 Java 라이브러리로 반복되는 getter, setter, toString ..등의 반복 메서드 작성 코드를 줄여주는 코드 다이어트 라이브러리입니다.

롬복 라이브러리가 제공하는 `@RequiredArgsConstructor` 기능을 사용하면 final이 붙은 필드를 모아서 생성자를 자동으로 만들어줍니다! 따라서 아래와 같이 간결하게 만들 수 있습니다.

```java
@Component
@RequiredArgsConstructor
public class OrderServiceImpl implements OrderService {

  private final MemberRepository memberRepository;
  private final DiscountPolicy discountPolicy;

}
```

최근에는 생성자를 딱 1개 두고, `@Autowired`를 생략하는 방법을 주로 사용합니다. 여기에 Lombok 라이브러리의 `@RequiredArgsConstructor` 함께 사용하면 기능은 다 제공하면서, 코드는 깔끔하게
사용할 수 있습니다.

---

## 조회 빈이 2개 이상 - 문제

기본적으로 `@Autowired`는 타입(Type)으로 조회를 합니다. 다음과 같은 코드가 있습니다.

```java
  @Autowired
  private DiscountPolicy discountPolicy
```

`@Autowired`는 타입으로 조회하기 때문에 다음 코드와 유사하게 동작합니다. 물론 실제론 더 많은 기능을 제공합니다.

```java
  ac.getBean(DiscountPolicy.class)
```

타입으로 조회를 할 때 선택된 빈이 2개 이상이면 문제가 발생합니다.

```java
  @Component
  public class FixDiscountPolicy implements DiscountPolicy { }
```

```java
  @Component
  public class RateDiscountPolicy implements DiscountPolicy { }
```

`DiscountPolicy`의 하위 타입인 `FixDiscountPolicy`와 `RateDiscountPolicy`를 스프링 빈으로 선언했습니다. 이 상태에서 의존관계 자동 주입을 실행하게 되면 `NoUniqueBeanDefinitionException` 오류가 발생하는 것을 볼 수 있습니다.  
왜냐하면 타입으로 조회를 한다고 했는데, `FixDiscountPolicy`와 `RateDiscountPolicy`의 타입이 `DiscountPolicy`로 동일하기 때문입니다. 오류 메시지에 아주 친절하게 '하나의 빈을 기대했는데, `fixDiscountPolicy`, `rateDiscountPolicy` 2개가 발견되었다'라고 알려줍니다.

이 때 하위타입으로 지정할 수도 있지만, 하위 타입으로 지정하는 것은 DIP(의존관계 역전 원칙)을 위배하고 유연성이 떨어집니다. 그리고 이름만 다르고, 완전히 동일한 타입의 스프링 빈이 2개 있을 수 있기 때문에 해결이 되지 않습니다.  
스프링 빈을 수동 등록해서 문제를 해결해도 되지만, 의존관계 자동 주입에서 해결하는 여러가지 방법이 있습니다.

---

## @Autowired 필드 명, @Qualifier, @Primary

