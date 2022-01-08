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