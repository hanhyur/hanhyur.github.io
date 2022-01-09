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

프로그램의 제어 흐름에 대한 권한은 모두 AppConfig가 가지고 있습니다. `OrderServiceImpl`도 AppConfig가 생성하고, OrderService 인터페이스의 다른 구현 객체를 생성하고 실행할 수 도 있습니다. `OrderServiceImpl`은 자기 로직만 실행합니다.

이렇듯 프로그램의 제어 흐름을 직접 제어하는 것이 아닌 외부에서 관리하는 것을 제어의 역전(IoC)이라고 합니다.

- 프레임워크 vs 라이브러리
  - 내가 작성한 코드를 제어하고, 대신 실행하면 프레임워크 (ex. JUnit)
  - 내가 작성한 코드가 직접 제어의 흐름을 담당하면 라이브러리

### 의존관계 주입 DI(Dependency Injection)

의존관계는 <b>정적인 클래스 의존 관계</b>와 <b>실행 시점에 결정되는 동적인 객체(인스턴스) 의존 관계</b> 둘을 분리하여 생각해야 합니다.


`OrderServiceImpl`은 `DiscountPolicy` 인터페이스에 의존하기 때문에, 어떤 구현 객체가 실제로 사용될 지 모릅니다.
