---
layout: post
date: 2022-01-08 12:22:00
title: "스프링 핵심 원리 이해2 2부"
description: "스프링 핵심 원리 - 기본편"
subject: Spring
category: [ spring ]
tags: [ spring, basic, oop ]
use_math: true
comments: true
---

[스프링 핵심 원리 - 기본편](https://inf.run/bMZx)을 공부하고 정리하는 포스트입니다.

---

# AppConfig 리팩터링

현재 AppConfig는 중복이 있고, 역할에 따른 구현이 잘 보이지 않습니다. 설정 정보, 구성 정보라는 것은 역할에 따른 구현이 한 눈에 보이는게 중요합니다.

```java
package hello.core;

import hello.core.discount.FixDiscountPolicy;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;
import hello.core.member.MemoryMemberRepository;
import hello.core.order.OrderService;
import hello.core.order.OrderServiceImpl;

public class AppConfig {

  public MemberService memberService() {
    return new MemberServiceImpl(new MemoryMemberRepository());
  }

  public OrderService orderService() {
    return new OrderServiceImpl(new MemoryMemberRepository(), new FixDiscountPolicy());
  }

}
```

현재 AppConfig 코드를 보면 위와 같습니다. `memberService`가 있으면 `MemberRespository`라는 역할이 보여야 하는데 보이지 않습니다. 하나씩 리팩터링 하겠습니다.

```java
package hello.core;

import hello.core.discount.DiscountPolicy;
import hello.core.discount.FixDiscountPolicy;
import hello.core.member.MemberRepository;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;
import hello.core.member.MemoryMemberRepository;
import hello.core.order.OrderService;
import hello.core.order.OrderServiceImpl;

public class AppConfig {

  public MemberService memberService() {
    return new MemberServiceImpl(memberRepository());
  }

  private MemberRepository memberRepository() {
    return new MemoryMemberRepository();
  }

  public OrderService orderService() {
    return new OrderServiceImpl(new MemoryMemberRepository(), new FixDiscountPolicy());
  }

}
```

이제 인터페이스에 반환해주는 MemberService 역할, MemberRepository 역할이 다 드러나게 되었습니다. 한 가지 안 드러난 것이 있습니다. `DiscountPolicy`입니다. 다음과 같이 바꿔줍니다.

```java
package hello.core;

import hello.core.discount.DiscountPolicy;
import hello.core.discount.FixDiscountPolicy;
import hello.core.member.MemberRepository;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;
import hello.core.member.MemoryMemberRepository;
import hello.core.order.OrderService;
import hello.core.order.OrderServiceImpl;

public class AppConfig {

  public MemberService memberService() {
    return new MemberServiceImpl(memberRepository());
  }

  private MemberRepository memberRepository() {
    return new MemoryMemberRepository();
  }

  public OrderService orderService() {
    return new OrderServiceImpl(memberRepository(), discountPolicy());
  }

  public DiscountPolicy discountPolicy() {
    return new FixDiscountPolicy();
  }

}
```

코드만 봐도 메서드 명만 봐도 각각의 역할이 다 드러납니다. 만일 `MemberRepository`를 메모리가 아닌 DB를 쓰고 싶다면 `memberRepository`만 수정하면 됩니다.

이러한 리팩터링을 통해 중복이 제거되고, `MemoryRepository`를 다른 구현체로 변경할 때 한 부분만 변경하면 됩니다.  
그리고 AppConfig를 보면 역할과 구현 클래스가 한 눈에 들어와 애플리케이션 전체 구성이 어떻게 되었는지 빠르게 파악할 수 있게 되었습니다.

---

# 새로운 구조와 할인 정책 적용

새롭게 만들어진 구조에서 정액 할인(FixDiscount)정책을 정률 할인(RateDiscount)정책으로 변경해보겠습니다. 어떤 부분을 변경하면 될까요?

기존에는 이것을 변경할 때 클라이언트 코드가 영향을 받았습니다. 하지만 AppConfig를 만들면서 애플리케이션이 크게 사용 영역과 구성하는 영역으로 분리되었습니다.

<img src="/assets/img/springcore/core27.png" width="70%" align="center"><br/>

객체를 생성하고 구성(Configuration)하는 영역은 사용 영역과 구분되어 있기 때문에 구성 영역만 수정하면 클라이언트에 영향을 주지않고 변경할 수 있습니다.

이제 AppConfig에서 다음과 같이 수정해줍니다.

```java
...

public class AppConfig {

  ...

  public DiscountPolicy discountPolicy() {
//    return new FixDiscountPolicy();
    return new RateDiscountPolicy();
  }

}
```

이제 수정을 했으니 해당 기능이 잘 적용되었는지 확인해보겠습니다. 

<img src="/assets/img/springcore/core28.png" width="70%" align="center"><br/>

정말 변경되서 적용되는지 확인하기 위해 기존의 할인 정책일 때와 비교해보겠습니다.

<img src="/assets/img/springcore/core29.png" width="70%" align="center"><br/>

이처럼 할인 정책을 변경하더라도, 애플리케이션의 구성 역할을 담당하는 부분만 변경하면 됩니다. 클라이언트 코드를 포함하여 사용 영역의 어떠한 코드도 손댈 필요가 없습니다.

이러한 구조로 인해 DIP(추상화 의존), OCP(확장성)을 만족하게 되었습니다.

---