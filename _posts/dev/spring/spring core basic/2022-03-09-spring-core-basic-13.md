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

