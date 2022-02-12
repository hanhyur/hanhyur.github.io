---
layout: post
date: 2022-02-11 20:04:00
title: "싱글톤 컨테이너 1부"
description: "스프링 핵심 원리 - 기본편"
subject: Spring
category: [ spring ]
tags: [ spring, basic, oop ]
use_math: true
comments: true
---

[스프링 핵심 원리 - 기본편](https://inf.run/bMZx)을 공부하고 정리하는 포스트입니다.

---

# 싱글톤 컨테이너

싱글톤 패턴이라는 말을 들어보셨을겁니다. 쉽게 말해서 객체 인스턴스가 현재 Java JVM 안에 하나만 존재해야 하는 패턴을 싱글톤 패턴이라고 합니다.

## 웹 애플리케이션과 싱글톤

우리가 자주쓰는 스프링은 어떻게 탄생한 것일까요? 스프링은 처음에 기업용 온라인 서비스 기술을 지원하기 위해서 탄생했습니다. 대부분의 스프링 애플리케이션은 웹 애플리케이션입니다. 지금은 웹 애플리케이션으로 많이 되어있는데 실제로 애플리케이션은 종류가 굉장히 다양합니다. 따라서 여러 종류의 애플리케이션 개발도 가능합니다.

이러한 웹 애플리케이션은 보통 여러 고객이 동시에 요청을 합니다.

<img src="/assets/img/springcore/core50.png" width="50%" align="center"><br/>

3명이 동시에 요청을 하면 DI 컨테이너(AppConfig)에서 요청에 맞춰서 반환을 해줍니다. 즉, 요청이 올 때마다 객체를 생성해서 응답을 해줍니다. 실제로 저렇게 되는지 확인해보겠습니다.

```java
package hello.core.singleton;

import hello.core.AppConfig;
import hello.core.member.MemberService;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;

public class SingletonTest {

  @Test
  @DisplayName("스프링 없는 순수 DI 컨테이너")
  void pureContainer() {
    AppConfig appConfig = new AppConfig();

    // 1. 조회 : 호출할 때 마다 객체를 생성
    MemberService memberService1 = appConfig.memberService();

    // 2. 조회 : 호출할 때 마다 객체를 생성
    MemberService memberService2 = appConfig.memberService();

    // 참조값이 다른 것을 확인
    System.out.println("memberService1 = " + memberService1);
    System.out.println("memberService2 = " + memberService2);
    
    // memberService1 != memberService2
    Assertions.assertThat(memberService1).isNotSameAs(memberService2);
  }
}
```

<img src="/assets/img/springcore/core51.png" width="50%" align="center"><br/>

실행 결과를 보면 `AppConfig`에게 요청을 할 때마다 다르게 생성되는 것을 확인할 수 있습니다. 즉, 요청할 때 마다 JVM 메모리에 다른 객체가 생성됩니다.

웹 애플리케이션은 고객의 요청이 매우 많다는 특징을 가지고 있습니다. 실제로 몇 만건의 요청이 한꺼번에 발생할 수 있는데 이럴 때 초 당 객체가 몇 만개가 생성되는 것은 당연히 말이 되지 않습니다.

따라서, 이를 해결하는 방법이 요청이 될 때 해당 객체 하나만 생성되고, 이 객체를 공유하도록 설계하는 <b>싱글톤 패턴</b>입니다.

---

## 싱글톤 패턴