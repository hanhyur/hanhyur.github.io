---
layout: post
date: 2022-02-11 20:04:00
title: "싱글톤 컨테이너 1부"
description: "스프링 핵심 원리 - 기본편"
subject: Spring
category: [ spring boot basic ]
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

싱글톤 패턴은 클래스의 인스턴스가 1개만 생성되는 것을 보장하는 디자인 패턴입니다. 하나의 JVM, 하나의 Java 서버 안에서는 객체 인스턴스가 1개만 생성된다는 것입니다. 방법은 간단합니다. 같은 객체 인스턴스를 2개 이상 생성하지 못하도록 막으면 됩니다.

다음은 싱글톤 패턴을 적용한 예제 코드입니다.

```java
package hello.core.singleton;

public class SingletonService {

  private static final SingletonService instance = new SingletonService();

  public static SingletonService getInstance() {
    return instance;
  }

  private SingletonService() {

  }

  public void logic() {
    System.out.println("Call Singleton logic");
  }
}
```

먼저, `private static final SingletonService instance = new SingletonService();`라고 자기 자신을 선언합니다. 자기 자신을 내부에 `private static`으로 가지고 있는데, 이렇게 하면 클래스 레벨에 올라가기 때문에 딱 하나만 존재하게 됩니다.  
그리고 조회를 하기 위해 `getInstance()`를 사용하는데, JVM이 동작할 때 내부적으로 실행되서 객체를 생성해서 instance에 참조로 넣어둡니다. 그 참조를 가져오는 것이 `getInstance()`입니다.  
외부에 감추기 위해서 이렇게 작성을 한다해도, 누군가가 `new`를 사용해서 만들 수도 있습니다. 이를 막기 위해서 `private SingletonService()`를 만들어서 막아둡니다.  
마지막으로 `logic()`을 추가해주면 싱글톤이 완성됩니다.

이제 싱글톤 패턴을 사용하는 테스트코드를 보도록 하겠습니다.

```java
@Test
@DisplayName("싱글톤 패턴을 적용한 객체 사용")
void SingletonServiceTest() {
  SingletonService singletonService1 = SingletonService.getInstance();
  SingletonService singletonService2 = SingletonService.getInstance();

  System.out.println("singletonService1 = " + singletonService1);
  System.out.println("singletonService2 = " + singletonService2);

  assertThat(singletonService1).isSameAs(singletonService2);
}
```

만약 `new SingletonService()`로 생성하려고하면 <b>private access</b>로 인해 생성할 수 없다는 compile 오류가 발생합니다. 따라서 `getInstance()`를 사용해서 조회를 합니다.  
이렇게 참조 값들을 확인해보면 같은 객체 인스턴스가 반환되는 것을 볼 수 있습니다.

<img src="/assets/img/springcore/core52.png" width="50%" align="center"><br/>

이 말은 이미 자바가 뜰 때 생성한 것을 가져다 사용한다는 것입니다.

싱글톤 패턴을 적용하기 위해서 기존의 AppConfig를 수정하면 될 꺼 같지만, 사실 스프링 컨테이너를 쓰면 스프링 컨테이너가 기본적으로 객체를 다 싱글톤으로 만들어서 관리해 줍니다.

싱글톤 패턴을 구현하는 방법은 굉장히 많습니다. 여기서 사용한 방법은 객체를 미리 생성해두는 가장 단순하고 안전한 방법을 사용했습니다.

### 싱글톤 패턴 문제점

이러한 싱글톤 패턴은 다음과 같은 문제점들이 존재합니다.

- 싱글톤 패턴을 구현하는 코드 자체가 많이 들어간다.
- 의존관계상 클라이언트가 구체 클래스에 의존한다. DIP를 위반한다.
- 클라이언트가 구체 클래스에 의존해서 OCP 원칙을 위반할 가능성이 높다.
- 테스트하기 어렵다.
- 내부 속성을 변경하거나 초기화 하기 어렵다.
- private 생성자로 자식 클래스를 만들기 어렵다.

결론적으로 유연성이 떨어져 안티패턴으로 불리기도 합니다.

---

## 싱글톤 컨테이너

스프링 컨테이너는 싱글톤 패턴의 문제점을 해결하면서 객체 인스턴스를 싱글톤으로 관리합니다. 바로 스프링 빈이 싱글톤으로 관리되는 빈입니다.

컨테이너 생성 과정을 생각해보면, 컨테이너는 객체를 하나만 생성해서 관리하는 것을 알 수 있습니다. 즉, 객체 인스턴스를 싱글톤으로 관리하는 것입니다. 이처럼 스프링 컨테이너는 싱글톤 컨테이너 역할을 하는데 이렇게 싱글톤 객체를 생성하고 관리하는 기능을 <b>싱글톤 레지스트리</b>라고 합니다.

이러한 기능 덕분에 싱글턴 패턴의 모든 단점을 해결하면서 객체를 싱글톤으로 유지할 수 있습니다.

```java
  @Test
  @DisplayName("스프링 컨테이너와 싱글톤")
  void springContainer() {
    ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

    MemberService memberService1 = ac.getBean("memberService", MemberService.class);
    MemberService memberService2 = ac.getBean("memberService", MemberService.class);

    // 참조값이 같은 것을 확인
    System.out.println("memberService1 = " + memberService1);
    System.out.println("memberService2 = " + memberService2);

    // memberService1 == memberService2
    assertThat(memberService1).isSameAs(memberService2);
  }
```

<img src="/assets/img/springcore/core53.png" width="50%" align="center"><br/>

결과에서 알 수 있듯이 처음 스프링 컨테이너에 등록한 빈을 반환해서 사용하기 때문에 같은 것을 확인할 수 있습니다. 그리고 해당 코드를 살펴보면 싱글톤과 관련된 코드가 하나도 없는 것을 볼 수 있습니다.

<img src="/assets/img/springcore/core54.png" width="50%" align="center"><br/>


이처럼 싱글톤 컨테이너를 적용하면 고객의 요청이 올 때 마다 객체를 생성하는 것이 아니라, 이미 만들어진 객체를 공유해서 효율적으로 재사용할 수 있습니다.  

스프링의 기본 빈 등록 방식은 싱글톤이지만, 싱글톤 방식'만' 지원하는 것은 아닙니다. 요청할 때마다 새로운 객체를 생성해서 반환하는 기능도 제공합니다.

---

## 싱글톤 방식의 주의점

싱글톤 패턴이든, 스프링 컨테이너를 사용하든, 객체 인스턴스를 하나만 생성해서 공유하는 싱글톤 방식은 여러 클라이언트가 하나의 같은 객체 인스턴스를 공유하기 때문에 <b>stateful</b>하게 설계해서는 안됩니다.

이게 무슨 말인가 하면, 무상태(stateless)하게 설계해야 하는데 특정 클라이언트에 의존적인 필드가 있어서는 안됩니다. 또한 특정 클라이언트가 값을 변경할 수 있는 필드가 있어서도 안됩니다. 그러기 위해서는 가급적 읽기만 가능해야 하는데, 필드 대신 자바에서 공유되지 않는, 지역변수, 파이미터, ThreadLocal 등을 사용해야 합니다.

상태를 유지할 경우에 발생하는 문제점을 예시와 테스트코드로 보겠습니다.

```java
package hello.core.singleton;

public class StatefulService {

  private int price;    // 상태를 유지하는 필드

  public void order(String name, int price) {
    System.out.println("name = " + name + " price = " + price);

    this.price = price;
  }

  public int getPrice() {
    return price;
  }
}
```

```java
package hello.core.singleton;

import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Bean;

class StatefulServiceTest {

  @Test
  void statefulServiceSingleton() {
    ApplicationContext ac = new AnnotationConfigApplicationContext(TestConfig.class);
    StatefulService statefulService1 = ac.getBean(StatefulService.class);
    StatefulService statefulService2 = ac.getBean(StatefulService.class);

    // Thread : A 사용자 10000원 주문
    statefulService1.order("userA", 10000);
    // Thread : B 사용자 20000원 주문
    statefulService1.order("userB", 20000);
    
    // ThreadA : 사용자 A 주문 금액 조회
    int price = statefulService1.getPrice();
    System.out.println("price = " + price);

    Assertions.assertThat(statefulService1.getPrice()).isEqualTo(20000);
  }

  static class TestConfig {

    @Bean
    public StatefulService statefulService() {
      return new StatefulService();
    }

  }

}
```

A 사용자가 10000원어치 주문을 하고 그 주문 금액을 가져와서 확인하려는데 중간에 B 사용자가 20000원으로 주문해서 끼어들었습니다. 그리고 테스트 결과를 살펴보면 가격이 B 사용자의 가격으로 변경된 것을 볼 수 있습니다.

<img src="/assets/img/springcore/core55.png" width="50%" align="center"><br/>

이처럼 stateful 객체는 같은 객체이기 때문에 다른 사람이 주문을 할 때마다 그 값으로 계속 변경됩니다. 특정 클라이언트가 공유하는 필드 값을 변경하는 것입니다.  

따라서, 항상 스프링 빈은 무상태(stateless)로 설계해야합니다.