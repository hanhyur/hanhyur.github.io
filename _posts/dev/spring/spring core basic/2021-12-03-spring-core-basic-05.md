---
layout: post
date: 2021-12-03 20:41:00
title: "스프링 핵심 원리 이해2 1부"
description: "스프링 핵심 원리 - 기본편"
subject: Spring
category: [ spring core basic ]
tags: [ spring, basic, oop ]
use_math: true
comments: true
---

[스프링 핵심 원리 - 기본편](https://inf.run/bMZx)을 공부하고 정리하는 포스트입니다.

---

# 객체 지향 원리 적용

기존의 코드에 좋은 객체 지향 원리를 적용해보자.

## 새로운 할인 정책 개발

기획자가 나타나서 새로운 할인 정책을 요구한다. 그래서 새로운 할인 정책을 적용하려니 여러 문제가 발생하게 된다. 우리는 이러한 문제를 해결하면서 스프링의 탄생을 이해하게 된다.

새로운 할인 정책의 시나리오를 먼저 살펴보자.

---

악덕 기획자 : 서비스 오픈 직전에 할인 정책을 지금처럼 고정 금액 할인이 아니라 좀 더 합리적인 주문 금액당 할인하는 정률% 할인으로 변경하고 싶어요. 예를 들어서 기존 정책은 VIP가 10000원을 주문하든 20000원을 주문하든 항상 1000원을 할인했는데, 이번에 새로 나온 정책은 10%로 지정해두면 고객이 10000원 주문시 1000원을 할인해주고, 20000원 주문시에 2000원을 할인해주는 거에요!

순진 개발자 : 제가 처음부터 고정 금액 할인은 아니라고 했잖아요.

악덕 기획자 : 애자일 소프트웨어 개발 선언<sup>[1](#footnote_1)</sup> 몰라요? “계획을 따르기보다 변화에 대응하기를”

순진 개발자 : … (하지만 난 유연한 설계가 가능하도록 객체지향 설계 원칙을 준수했지 후후)

---

이제 새로운 정률 할인 정책을 추가해보자. 객체지향 설계 원칙을 잘 준수했다면 우리는 `RateDiscountPolicy`를 만들어서 바꿔주기만 하면 된다.

<img src="/assets/img/springcore/core17.png" width="70%" align="center"><br/>

먼저 빠르게 코드를 작성한다.

```java
package hello.core.discount;

import hello.core.member.Grade;
import hello.core.member.Member;

public class RateDiscountPolicy implements DiscountPolicy {

  private int discountPercent = 10;

  @Override
  public int discount(Member member, int price) {
    if(member.getGrade() == Grade.VIP) {

      return price * discountPercent / 100;
    } else {
      return 0;
    }
  }

}
```

이렇게 할인 정책 코드를 완성했으면 이제 테스트 코드를 작성해서 확인해보자.

```java
package hello.core.discount;

import hello.core.member.Grade;
import hello.core.member.Member;
import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;

class RateDiscountPolicyTest {

  RateDiscountPolicy discountPolicy = new RateDiscountPolicy();

  @Test
  @DisplayName("VIP는 10% 할인이 적용되어야 한다.")
  void vip_o() {
    //given
    Member member = new Member(1L, "memberVIP", Grade.VIP);

    //when
    int discount = discountPolicy.discount(member, 10000);

    //then
    Assertions.assertThat(discount).isEqualTo(1000);
  }
  
  @Test
  @DisplayName("VIP가 아니면 할인이 적용되지 않아야 한다.")
  void vip_x() {
    //given
    Member member = new Member(2L, "memberVIP", Grade.BASIC);

    //when
    int discount = discountPolicy.discount(member, 10000);

    //then
    Assertions.assertThat(discount).isEqualTo(1000);
  }
}
```

성공 케이스를 확인하는 것도 중요하지만 실패 케이스를 확인하는 것도 중요하다. 아래의 테스트 코드는 실제로 0이 나와야 하는데 1000이 나온다고 가정하고 작성되었다. 이제 결과를 확인해보자.

<img src="/assets/img/springcore/core18.png" width="70%" align="center"><br/>

올바르게 작성된 성공 케이스는 성공을 나타냈지만 실패 케이스는 오류가 출력되었다. 오류 부분을 잘 보면 기대값은 1000이였는데 실제 값은 0이어서 실패했다고 나와있다. 이제 실패한 케이스의 기대값을 0으로 바꾸고 돌려보자. 성공하는 것을 확인할 수 있다.

<img src="/assets/img/springcore/core19.png" width="70%" align="center"><br/>

---

## 새로운 할인 정책 적용과 문제점

이제 만든 할인 정책을 적용해보겠다. 할인 정책을 적용하기 위해서는 `OrderServiceImpl`에 들어가서 기존에 있던 `DiscountPolicy`인 `FixDiscountPolicy` 대신에 `RateDiscountPolicy`로 변경해주면 된다. 

```java
package hello.core.order;

import hello.core.discount.DiscountPolicy;
import hello.core.discount.FixDiscountPolicy;
import hello.core.discount.RateDiscountPolicy;
import hello.core.member.Member;
import hello.core.member.MemberRepository;
import hello.core.member.MemoryMemberRepository;

public class OrderServiceImpl implements OrderService{

  private final MemberRepository memberRepository = new MemoryMemberRepository();
//  private final DiscountPolicy discountPolicy = new FixDiscountPolicy();
  private final DiscountPolicy discountPolicy = new RateDiscountPolicy();

  @Override
  public Order createOrder(Long memberId, String itemName, int itemPrice) {
    Member member = memberRepository.findById(memberId);
    int discountPrice = discountPolicy.discount(member, itemPrice);

    return new Order(memberId, itemName, itemPrice, discountPrice);
  }
}
```

여기서 문제점이 발견되었다. 지금까지 잘 따라왔다면 문제점이 무엇인지 바로 알아차렸을 것이다. 하나씩 확인해보자.

- 역할과 구현을 충실하게 분리하였는가? -> OK
- 다형성도 활용하고, 인터페이스와 구현 객체를 분리하였는가? -> OK
- OCP, DIP 같은 객체 지향 설계 원칙을 준수하였는가? -> OK 인 것 같지만 NO

우리는 분명히 객체 지향 설계 원칙을 준수하면서 작성한 것 같은데 무엇이 문제인 것일까?

### DIP

주문 서비스 클라이언트(`OrderServiceImpl`)는 `DiscountPolicy` 인터페이스에 의존하면서 DIP를 지킨 것 같지만 자세히 살펴보면 문제점이 보인다.  
바로 클래스 의존관계를 분석해보면, 추상(인터페이스) 뿐만 아니라 <b>구체(구현) 클래스에도 의존</b>하고 있는 것이다.

- 추상(인터페이스) 의존 : DiscountPolicy
- 구체(구현) 클래스 : FixDiscountPolicy, RateDiscountPolicy

### OCP

코드를 변경하지 않고 확장할 수 있어야 하는데, 기능을 확장해서 변경하면 클라이언트 코드에 영향을 준다.

클래스 다이어그램을 보면서 좀 더 살펴보자.

<img src="/assets/img/springcore/core20.png" width="70%" align="center"><br/>

지금까지 우리가 예상한 의존은 위의 그림과 같았다. 하지만 실제 의존관계는 아래와 같았던 것이다. 즉, DIP를 위반하고 있었다.

<img src="/assets/img/springcore/core21.png" width="70%" align="center"><br/>

그래서 정책을 바꾸기 위해서는 `OrderServiceImpl`의 코드도 변경을 해야하는 것이다. OCP 위반을 하는 것이다.

<img src="/assets/img/springcore/core22.png" width="70%" align="center"><br/>

이것을 해결하기 위해서는 우리가 처음 예상했던 것처럼 인터페이스만 의존하도록 만들어야 한다. 현재 클라이언트 코드는 인터페이스 뿐만 아니라 구체 클래스도 의존한다.

```java
package hello.core.order;

import hello.core.discount.DiscountPolicy;
import hello.core.member.Member;
import hello.core.member.MemberRepository;
import hello.core.member.MemoryMemberRepository;

public class OrderServiceImpl implements OrderService{

  private final MemberRepository memberRepository = new MemoryMemberRepository();
  private DiscountPolicy discountPolicy;

  @Override
  public Order createOrder(Long memberId, String itemName, int itemPrice) {
    Member member = memberRepository.findById(memberId);
    int discountPrice = discountPolicy.discount(member, itemPrice);

    return new Order(memberId, itemName, itemPrice, discountPrice);
  }
}
```

이렇게 바꾸면 인터페이스만 의존하도록 변경된 것이다. 하지만 여기서 테스트 코드를 실행해서 확인하면?

<img src="/assets/img/springcore/core23.png" width="70%" align="center"><br/>

NullPointException이 발생하는 것을 확인할 수 있다. 생각하면 당연한 것이 `discountPolicy`에 아무런 값이 할당되지 않으니 발생하는 것이다.

이 문제를 어떻게 해결할 수 있을까? 이 문제를 해결하려면 누군가가 클라이언트인 `OrderServiceImpl`에 `DiscountPolicy`의 구현 객체를 대신 생성하고 주입해주어야 한다.

---

## 관심사의 분리

애플리케이션을 하나의 공연이라고 생각하고 각각의 인터페이스를 배역이라고 할 때, 배역에 맞는 배우를 선택하는 것은 누가 하는가?  
로미오와 줄리엣 공연을 하면 로미오 역할과 줄리엣 역할을 누가 할지는 배우가 아니라 공연 측, 공연의 기획자가 결정하는 것이다.

왜 이 이야기를 하는가? 우리의 이전 코드는 배우가 직접 상대 배우를 지정하는 것과 같은 상태였다. 배우에게 많은 책임이 주어지는 것이다.

### 관심사를 분리하자

배우는 본인의 역할인 배역을 수행하는 것에만 집중해야 한다. 디카프리오는 어떤 여자 주인공이 선택되더라도 똑같이 공연을 할 수 있어야 한다.
공연을 구성하고, 담당 배우를 섭외하고, 역할에 맞는 배우를 지정하는 책임을 담당하는 별도의 <b>"공연 기획자"</b>가 나올 시점이다.
공연 기획자를 만들고, 배우와 공연 기획자의 책임을 확실히 분리해야 한다.

## AppConfig 등장

애플리케이션의 전체 동작 방식을 구성(config)하기 위해, <b>구현 객체를 생성하고 연결</b>하는 책임을 가지는 별도의 설정 클래스를 만든다.

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

`AppConfig`는 애플리케이션의 실제 동작에 필요한 구현 객체들을 생성한다. 그리고 생성한 객체 인스턴스의 참조(레퍼런스)를 생성자를 통해서 주입(연결)해준다.

```java
package hello.core.member;

public class MemberServiceImpl implements MemberService {

  private final MemberRepository memberRepository;

  public MemberServiceImpl(MemberRepository memberRepository) {
    this.memberRepository = memberRepository;
  }

  @Override
  public void join(Member member) {
    memberRepository.save(member);
  }

  @Override
  public Member findMember(Long memberId) {
    return memberRepository.findById(memberId);
  }

}
```

```java
package hello.core.order;

import hello.core.discount.DiscountPolicy;
import hello.core.member.Member;
import hello.core.member.MemberRepository;
import hello.core.member.MemoryMemberRepository;

public class OrderServiceImpl implements OrderService{

  private final MemberRepository memberRepository;
  private final DiscountPolicy discountPolicy;

  public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
    this.memberRepository = memberRepository;
    this.discountPolicy = discountPolicy;
  }

  @Override
  public Order createOrder(Long memberId, String itemName, int itemPrice) {
    Member member = memberRepository.findById(memberId);
    int discountPrice = discountPolicy.discount(member, itemPrice);

    return new Order(memberId, itemName, itemPrice, discountPrice);
  }
}
```

이제 생성자를 만들어주었다. 설계의 변경으로 더 이상 구현체에 의존을 하지 않게 되었고, 각각의 입장에서는 어떤 구현체가 들어올지 알 수 없다. 단지 실행만 되면 되는 것이다.

<img src="/assets/img/springcore/core24.png" width="70%" align="center"><br/>

DIP가 완성되었다. `MemberServiceImpl`은 `MemberRepository`인 추상에만 의존하면 된다. 구체 클래스는 더 이상 몰라도 된다. 관심사의 분리가 이루어진 것이다.

## AppConfig 실행

실제 적용하고 실행해서 결과를 확인해보자.

```java
package hello.core;

import hello.core.member.Grade;
import hello.core.member.Member;
import hello.core.member.MemberService;

public class MemberApp {

  public static void main(String[] args) {
    AppConfig appConfig = new AppConfig();
    MemberService memberService = appConfig.memberService();
    Member member = new Member(1L, "memberA", Grade.VIP);
    memberService.join(member);

    Member findMember = memberService.findMember(1L);
    System.out.println("new member = " + member.getName());
    System.out.println("find member = " + findMember.getName());
  }

}
```

기존에는 `memberService`를 직접 만들었지만, 이제는 `AppConfig`로부터 의존관계를 주입받는다. `OrderApp`도 수정해주자.

```java
package hello.core;

import hello.core.member.Grade;
import hello.core.member.Member;
import hello.core.member.MemberService;
import hello.core.order.Order;
import hello.core.order.OrderService;

public class OrderApp {

  public static void main(String[] args) {
    AppConfig appConfig = new AppConfig();
    MemberService memberService = appConfig.memberService();
    OrderService orderService = appConfig.orderService();

    Long memberId = 1L;
    Member member = new Member(memberId, "memberA", Grade.VIP);
    memberService.join(member);

    Order order = orderService.createOrder(memberId, "itemA", 10000);

    System.out.println("order = " + order);
    System.out.println("order.calculatePrice = " + order.calculatePrice());
  }

}
```

<img src="/assets/img/springcore/core25.png" width="70%" align="center"><br/>

둘 다 정상적으로 실행되는 것을 확인할 수 있다. 물론 여기서 끝이 아니라 테스트 코드도 수정해주자.

```java
package hello.core.member;

import hello.core.AppConfig;
import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

public class MemberServiceTest {

  MemberService memberService;

  @BeforeEach
  public void beforeEach() {
    AppConfig appConfig = new AppConfig();
    memberService = appConfig.memberService();
  }

  @Test
  void join() {
    //given
    Member member = new Member(1L, "memberA", Grade.VIP);

    //when
    memberService.join(member);
    Member findMember = memberService.findMember(1L);

    //then
    Assertions.assertThat(member).isEqualTo(findMember);

  }

}
```

```java
package hello.core.order;

import hello.core.AppConfig;
import hello.core.member.Grade;
import hello.core.member.Member;
import hello.core.member.MemberService;
import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

public class OrderServiceTest {

  MemberService memberService;
  OrderService orderService;

  @BeforeEach
  public void beforeEach() {
    AppConfig appConfig = new AppConfig();
    memberService = appConfig.memberService();
    orderService = appConfig.orderService();
  }

  @Test
  void createOrder() {
    Long memberId = 1L;
    Member member = new Member(memberId, "memberA", Grade.VIP);
    memberService.join(member);

    Order order = orderService.createOrder(memberId, "itemA", 10000);
    Assertions.assertThat(order.getDiscountPrice()).isEqualTo(1000);
  }

}
```

`@BeforeEach`는 테스트 코드가 실행되기 전에 먼저 호출된다. 수정을 하고 난 후 실행해보면 정상적으로 작동하는 것을 확인할 수 있다.

<img src="/assets/img/springcore/core26.png" width="70%" align="center"><br/>

---

정리하자면 AppConfig는 공연 기획자다. 배역에 맞는 담당 배우를 선택하듯이 구체 클래스를 선택하고, 애플리케이션이 어떻게 동작할지 전체 구성을 책임진다.

---
<a name="footnote_1">1</a> : 애자일 소프트웨어 개발 선언 https://agilemanifesto.org/iso/ko/manifesto.html