---
layout: post
date: 2022-01-08 12:22:00
title: "스프링 핵심 원리 이해2 2부"
description: "스프링 핵심 원리 - 기본편"
subject: Spring
category: [ spring core basic ]
tags: [ spring, basic, oop ]
use_math: true
comments: true
---

[스프링 핵심 원리 - 기본편](https://inf.run/bMZx)을 공부하고 정리하는 포스트입니다.

---

## AppConfig 리팩터링

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

## 새로운 구조와 할인 정책 적용

새롭게 만들어진 구조에서 정액 할인(FixDiscount)정책을 정률 할인(RateDiscount)정책으로 변경해보겠습니다. 어떤 부분을 변경하면 될까요?

기존에는 이것을 변경할 때 클라이언트 코드가 영향을 받았습니다. 하지만 AppConfig를 만들면서 애플리케이션이 크게 사용 영역과 구성하는 영역으로 분리되었습니다.

<img src="https://user-images.githubusercontent.com/39683512/160283065-db502bc1-d5f7-41e0-a9f8-6ec050cdc452.png" width="70%" align="center"><br/>

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

<img src="https://user-images.githubusercontent.com/39683512/160283066-abe7defe-2e7c-49f1-b1d5-ed62efad0ac0.png" width="70%" align="center"><br/>

정말 변경되서 적용되는지 확인하기 위해 기존의 할인 정책일 때와 비교해보겠습니다.

<img src="https://user-images.githubusercontent.com/39683512/160283067-3bce736d-a967-4396-baa1-f64f5ed67f55.png" width="70%" align="center"><br/>

이처럼 할인 정책을 변경하더라도, 애플리케이션의 구성 역할을 담당하는 부분만 변경하면 됩니다. 클라이언트 코드를 포함하여 사용 영역의 어떠한 코드도 손댈 필요가 없습니다.

이러한 구조로 인해 DIP(추상화 의존), OCP(확장성)을 만족하게 되었습니다.

---

## 전체 흐름 정리

지금까지의 흐름을 한번 정리해보겠습니다.

먼저, 새로운 할인 정책을 개발했습니다. 다형성 덕분에 새로운 정률 할인 정책 코드를 추가로 개발하는 것까지 아무런 문제가 없었습니다.

하지만 새로운 할인 정책을 적용하려니 클라이언트 코드인 주문 서비스 구현체도 함께 변경해야 되는, OCP를 위반하는 문제가 발생했습니다. 왜냐하면 주문 서비스 클라이언트가 `DiscountPolicy` 인터페이스와 구체(구현) 클래스인 `FixDiscountPolicy`도 함께 의존하고 있었기 때문입니다. 이는 DIP 위반입니다.

왜 이런 문제가 발생했는지 살펴보니, 클라이언트 코드인 주문 서비스 구현체가 너무 많은 역할을 하고 있었습니다. 이를 해결하기 위해 관심사를 분리했습니다.

기존에는 클라이언트가 의존하는 서버 구현 객체를 직접 생성하고, 실행도 했습니다. 애플리케이션을 하나의 공연이라고 생각하면 이러한 행동은 남자 주인공 역할의 배우가 공연도 하고, 동시에 여자 주인공 역할의 배우도 직접 초빙하는 등 다양한 책임을 가진 것과 같았습니다.

현실에서는 공연을 구성하고, 담당 배우를 섭외하고 지정하는 책임을 담당하는 별도의 <b>공연 기획자</b>가 존재합니다. 이러한 공연 기획자의 역할을 하는 `AppConfig`를 만들어주었습니다. `AppConfig`는 애플리케이션의 전체 동작 방식을 구성(Config)하기 위해, "구현 객체를 생성하고 연결"하는 책임을 가집니다.

이제 클라이언트 객체들은 자신의 역할을 실행하는 것만 집중하면 되고, 결과적으로 권한은 줄어들었지만 책임은 명확해졌습니다.

이어서 AppConfig를 리팩터링했습니다. 객체지향에서 항상 중요한 것이 구성 정보에서 역할과 구현을 명확하게 분리해야 한다고 했습니다. 이를 위해 역할이 잘 들어나도록 하고 중복을 제거해주었습니다.

구조를 이쁘게 변경하고 난 후 새로운 할인 정책을 적용해보았습니다. AppConfig가 등장하면서 애플리케이션이 "사용 영역"과 "구성 영역"으로 분리되었습니다. 할인 정책을 변경하기 위해서 AppConfig만 수정을 하면되고 클라이언트 코드와 사용 영역을 건드릴 필요가 없었습니다. 즉, DIP와 OCP를 지킬 수 있게 되었습니다.

---

## 좋은 객체 지향 설계의 5가지 원칙의 적용

지금까지 만든 애플리케이션에 좋은 객체 지향 설계의 5가지 원칙이 어떻게 적용되었는지 정리해보겠습니다. 현재 크게 3가지 SRP, DIP, OCP가 적용된 것을 볼 수 있습니다.

### SRP 단일 책임 원칙 - 한 클래스는 하나의 책임만 가져야 한다.

처음 만든 클라이언트 객체는 직접 구현 객체를 생성하고 연결하고 실행하는 다양한 책임을 가지고 있었습니다. SRP 단일 책임 원칙을 따르기 위해서 관심사를 분리하였습니다.

구현 객체를 생성하고 연결하는 책임을 AppConfig가 담당하도록 넘겨주었고, 클라이언트 객체는 실행하는 책임만 담당하도록 설계를 변경했습니다.

### DIP 의존관계 역전 원칙 - 프로그래머는 추상화에 의존해야하고 구체화에 의존하면 안된다.

새로운 할인 정책을 개발해서 적용하려니, 클라이언트 코드도 함께 변경해야 했습니다. 기존 클라이언트 코드인 `OrderServiceImpl`은 DIP를 지키며 `DiscountPolicy` 추상화 인터페이스에 의존하는 것 같았지만, 실제론 `FixDiscountPolicy`라는 구체화 구현 클래스에도 함께 의존하고 있었습니다. 추상화 뿐만 아니라 구체화 클래스도 함께 의존하고 있었습니다.

이를 해결하기 위해 클라이언트 코드가 `DiscountPolicy` 추상화 인터페이스에만 의존하도록 코드를 변경하였지만, 인터페이스만으로는 아무것도 실행할 수 없습니다. 실제로 `NullPointException`이 발생하는 것을 볼 수 있었습니다.

그래서 전체 애플리케이션을 구성하는 AppConfig가 `FixDiscountPolicy` 객체 인스턴스를 클라이언트 코드 대신 생성하여 클라이언트 코드에 의존관계를 주입하도록 변경했습니다. 이렇게 함으로서 DIP 원칙을 따르면서 문제도 해결했습니다.

### OCP - 소프트웨어 요소는 확장에는 열려 있으나 변경에는 닫혀 있어야 한다.

다형성을 사용하고 클라이언트가 DIP를 잘 지키면 OCP가 잘 적용될 가능성이 열립니다.

애플리케이션을 사용 영역과 구성 영역으로 나누었고, 새로운 할인 정책으로 변경할 때도 구성 영역에 해당하는 AppConfig가 의존관계를 `FixDiscountPolicy`에서 `RateDiscountPolicy`로 변경해서 클라이언트 코드에 주입하므로 클라이언트 코드는 직접 변경하지 않아도 됩니다.

이렇게 확장은 열려 있으나 변경은 닫혀 있어야 한다는 OCP 원칙을 지킬 수 있게 되었습니다.

---

## IoC, DI, 그리고 컨테이너

### 제어의 역전 IoC(Inversion of Control)

스프링을 학습하다보면 제어의 역전 IoC라는 말을 많이 들을 수 있습니다. 이 제어의 역전은 스프링에만 국한된 게 아닌 흔히 개발자들은 직접 다 컨트롤하고 제어하는 스타일로 개발하는데, 제어의 역전이라는 개념은 프레임워크나 툴이 대신 코드를 호출해 주는 것을 말합니다. 말 그대로 컨트롤, 제어권이 역전되었다는 의미입니다.

기존 프로그램은 클라이언트 구현 객체가 스스로 필요한 서버 구현 객체를 생성하고, 연결하고, 실행했습니다. 한 마디로 구현 객체가 흐름을 스스로 제어했습니다. 이러한 흐름은 개발자 입장에서 자연스러운 흐름입니다.

반면에 AppConfig가 등장한 이후로 구현 객체는 자신의 로직을 실행하는 역할만 담당하고 프로그램의 제어 흐름은 AppConfig가 가져갑니다. 예를 들어 OOServiceImpl은 필요한 인터페이스들을 호출하지만 어떤 구현 객체들이 실행되는지는 모릅니다.

프로그램의 제어 흐름에 대한 권한은 모두 AppConfig가 가지고 있습니다. 심지어 `OrderServiceImpl`도 AppConfig가 생성하고, OrderService 인터페이스의 다른 구현 객체를 생성하고 실행할 수 도 있습니다. `OrderServiceImpl`은 자기 로직만 실행합니다.

이렇듯 프로그램의 제어 흐름을 직접 제어하는 것이 아닌 외부에서 관리하는 것을 제어의 역전(IoC)이라고 합니다.

- 프레임워크 vs 라이브러리
  - 내가 작성한 코드를 제어하고, 대신 실행하면 프레임워크 (ex. JUnit)
  - 내가 작성한 코드가 직접 제어의 흐름을 담당하면 라이브러리

### 의존관계 주입 DI(Dependency Injection)

지금까지 작성한 코드에서 `OrderServiceImpl`은 처음에는 구현체까지 의존했지만 지금은 인터페이스에만 의존하도록 변경했습니다. 이제는 `DiscountPolicy` 인터페이스에 의존하기 때문에, 어떤 구현 객체가 실제로 사용될 지 모릅니다. 

이러한 의존관계는 <b>정적인 클래스 의존 관계</b>와 <b>실행 시점에 결정되는 동적인 객체(인스턴스) 의존 관계</b> 둘을 분리하여 생각해야 합니다.

먼저 "정적인 클래스 의존관계"는 클래스가 사용하는 import 코드만 보고 의존관계를 쉽게 판단할 수 있습니다. 정적인 의존관계는 애플리케이션을 실행하지 않고도 분석할 수 있습니다. 

<img src="https://user-images.githubusercontent.com/39683512/160283071-bffb97f2-fd54-48cb-a690-b9837425c64a.png" width="70%" align="center"><br/>

툴을 이용하여 위와 같은 다이어그램도 볼 수 있습니다. 이처럼 실행하지 않고도 판단할 수 있습니다.  
아래의 클래스 다이어그램을 보면 `OrderServiceImpl`은 `MemberRepository`, `DiscountPolicy`에 의존한다는 것을 알 수 있습니다. 하지만 이러한 클래스 의존관계 만으로는 실제로 어떤 객체가 주입되는지는 알 수 없습니다.

<img src="https://user-images.githubusercontent.com/39683512/160283043-faffef1f-a72d-49bd-a120-5ab52749f63d.png" width="70%" align="center"><br/>

무엇이 주입되는지는 실제로 실행해봐야 알 수 있습니다. 이러한 의존 관계를 "동적인 객체 의존 관계"라고 합니다. 아래의 객체 다이어그램을 보면 됩니다.

<img src="https://user-images.githubusercontent.com/39683512/160283044-d993ab6e-3dfa-4c1b-803a-16fb830cbb2f.png" width="70%" align="center"><br/>

이러한 객체 다이어그램은 애플리케이션이 실행될 때마다 동적으로 변경됩니다. 애플리케이션 런타임에 외부에서 실제 구현 객체를 생성하고 클라이언트에 전달해서 클라이언트와 서버의 실제 의존관계가 연결 되는 것을 "의존관계 주입"이라고 합니다.

객체 인스턴스를 생성하고, 그 참조값을 전달해서 연결하는데 알다시피 자바는 레퍼런스로 객체 인스턴스들이 연결됩니다.

의존관계 주입을 사용하면 클라이언트 코드를 변경하지 않고, 클라이언트가 호출하는 대상의 타입 인스턴스를 변경할 수 있습니다. 다르게 말하면 정적인 클래스 의존관계를 변경하지 않고, 동적인 객체 인스턴스 의존관계를 쉽게 변경할 수 있습니다.

### IoC 컨테이너, DI 컨테이너

AppConfig 처럼 객체를 생성하고 관리하면서 의존관계를 연결해 주는 것을 IoC 컨테이너 또는 DI 컨테이너라고 합니다. 최근에는 의존관계 주입에 초점을 맞춰 주로 DI 컨테이너라고 합니다.

---

## 스프링으로 전환하기

지금까지는 순수한 자바 코드만으로 DI를 적용했습니다. 이제부터는 스프링을 사용해보겠습니다.

먼저, AppConfig를 스프링 기반으로 변경해보겠습니다.

```java
package hello.core;

...
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

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

AppConfig가 애플리케이션의 구성정보를 담당한다는 것을 알리기 위해 @Configuration 애노테이션을 추가해줍니다. 애노테이션을 추가할 때는 import 되는 부분을 항상 주의해야 합니다. `org.springframework.context.annotation.Configuration`가 맞는지 확인하도록 합시다. 그리고 @Bean 애노테이션을 추가해줍니다.

스프링에서는 애플리케이션의 설정정보, 구성정보를 담당하는 것에 @Configuration을 적어주어야 합니다. 그리고 각 메서드에 @Bean을 추가함으로써 스프링 컨테이너에 등록이 됩니다.

이제 실제 스프링을 사용해보겠습니다. MemberApp을 AppConfig 버전에서 스프링 버전으로 변경해보겠습니다.

```java
package hello.core;

import hello.core.member.Grade;
import hello.core.member.Member;
import hello.core.member.MemberService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class MemberApp {

  public static void main(String[] args) {
//    AppConfig appConfig = new AppConfig();
//    MemberService memberService = appConfig.memberService();

    ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
    MemberService memberService = applicationContext.getBean("memberService", MemberService.class);

    Member member = new Member(1L, "memberA", Grade.VIP);
    memberService.join(member);

    Member findMember = memberService.findMember(1L);
    System.out.println("new member = " + member.getName());
    System.out.println("find member = " + findMember.getName());
  }

}
```

스프링은 `ApplicationContext`라는 것으로 시작합니다. `ApplicationContext`가 모든 객체들을 관리해주는 스프링 컨테이너라고 보면 됩니다. `ApplicationContext`를 생성할 때는 `new AnnotationConfigApplicationContext()`를 적어주어야 합니다. 그리고 파라미터는 우리가 만든 설정 정보인 AppConfig.class를 추가해주면 됩니다.  
이렇게 하면 스프링 컨테이너에 생성한 객체, @Bean이 붙은 것들을 다 넣어서 관리해줍니다.

기존에는 AppConfig에서 직접 찾아왔지만 이제는 스프링 컨테이너를 통해서 찾아옵니다. `getBean()`에서 찾을 객체의 이름과 타입을 추가해 줍니다. 이 때 객체의 이름은 기본적으로 메서드의 이름으로 등록되어 있습니다.

이제 실행해보겠습니다.

<img src="https://user-images.githubusercontent.com/39683512/160283071-bffb97f2-fd54-48cb-a690-b9837425c64a.png" width="70%" align="center"><br/>

기존과 다르게 로그가 나온 것을 볼 수 있습니다. Creating shared instance of singleton bean 이후에 메서드의 이름이 들어가 있는 것을 볼 수 있습니다. 즉 컨테이너에 해당 이름으로 등록된 것을 볼 수 있습니다. 이 후에는 기존과 동일하게 실행된 결과를 볼 수 있습니다.

마찬가지로 OrderApp도 변경해보겠습니다.

```java
package hello.core;

import hello.core.member.Grade;
import hello.core.member.Member;
import hello.core.member.MemberService;
import hello.core.order.Order;
import hello.core.order.OrderService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class OrderApp {

  public static void main(String[] args) {

//    AppConfig appConfig = new AppConfig();
//    MemberService memberService = appConfig.memberService();
//    OrderService orderService = appConfig.orderService();

    ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
    MemberService memberService = applicationContext.getBean("memberService", MemberService.class);
    OrderService orderService = applicationContext.getBean("orderService", OrderService.class);

    Long memberId = 1L;
    Member member = new Member(memberId, "memberA", Grade.VIP);
    memberService.join(member);

    Order order = orderService.createOrder(memberId, "itemA", 20000);

    System.out.println("order = " + order);
    System.out.println("order.calculatePrice = " + order.calculatePrice());
  }

}
```

이제 실행해보겠습니다. 똑같이 등록되고 결과가 나오는 것을 볼 수 있습니다.

<img src="https://user-images.githubusercontent.com/39683512/160283072-cab3d6f9-5ed3-4fec-9ada-6ddd443f45ef.png" width="70%" align="center"><br/>

### 스프링 컨테이너

`ApplicationContext`를 스프링 컨테이너라고 합니다. 기존에는 개발자가 AppConfig를 사용해서 직접 객체를 생성하고 DI를 해줬지만, 이제는 스프링 컨테이너를 사용해야 합니다.

이 스프링 컨테이너는 `@Configuration`이 붙은 클래스를 설정(구성) 정보로 사용합니다. 여기서 `@Bean`이 붙은 메서드를 모두 호출해서 반환된 객체를 스프링 컨테이너에 등록합니다. 이렇게 등록된 객체들을 <b>스프링 빈</b>이라고 합니다.

스프링 빈은 `@Bean`이 붙은 메서드 이름을 스프링 빈의 이름으로 사용합니다. 물론 `@Bean(name = '')`과 같은 형식으로 변경할 수 있습니다. 하지만 관례상 default를 사용하는게 좋습니다.

이전까지는 AppConfig를 사용해서 필요한 객체를 직접 조회했지만, 이제는 `applicationContext.getBean()` 메서드를 사용해서 스프링 컨테이너를 통해서 찾아야 합니다. 이름 그대로 빈을 찾는 메서드입니다.

기존에는 개발자가 직접 자바코드로 모든 것을 했지만 이제는 스프링에게 환경 정보를 주고 스프링에서 모든 것을 관리하고 필요한 스프링 빈만 찾아서 사용하면 됩니다.