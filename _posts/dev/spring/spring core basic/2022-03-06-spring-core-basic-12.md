---
layout: post
date: 2022-03-06 22:00:00
title: "의존관계 자동 주입 1부"
description: "스프링 핵심 원리 - 기본편"
subject: Spring
category: [spring]
tags: [spring, basic, oop]
use_math: true
comments: true
---

[스프링 핵심 원리 - 기본편](https://inf.run/bMZx)을 공부하고 정리하는 포스트입니다.

---

# 의존관계 자동 주입

의존관계를 주입하는 방법에는 다양한 방법이 있습니다. 그 중에서 좋은 방법과 최신 트렌드, 주입 시 문제점 등을 알아보겠습니다.

## 다양한 의존관계 주입 방법

의존관계 주입에는 크게 4가지 방법이 있습니다.

- 생성자 주입
- 수정자 주입(setter 주입)
- 필드 주입
- 일반 메서드 주입

### 생성자 주입

이름 그대로 생성자를 통해서 의존관계를 주입하는 방법입니다. 생성자 호출시점에 단 1번만 호출되는 것이 보장되고, <b>불변, 필수</b> 의존관계에 사용된다는 특징을 가지고 있습니다.

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
```

스프링이 컴포넌트 스캔을 할 때 생성자 호출 시점에 생성자(OrderServiceImpl)에 `@Autowired`가 있는 것을 보고 스프링 컨테이너에서 'MemberRepository'와 'DiscountPolicy'를 꺼내서 주입해줍니다.

생성자를 통해서만 주입을 해주기 때문에 외부에서 수정할 수 있는 방법이 없습니다. 이처럼 나의 의도를 유지하기 위해서, 처음 계획한 연관관계를 바꾸지 않기 위해 수정하는 메서드가 존재해서는 안됩니다. 따라서 <b>불변</b>이라는 특징이 정말 중요합니다.

한 가지 중요한 것이 있는데, 생성자가 단 1개만 있으면 `@Autowired`를 생략할 수 있습니다. 단, 스프링 빈에만 해당합니다.

```java
@Component
public class OrderServiceImpl implements OrderService{

  private final MemberRepository memberRepository;
  private final DiscountPolicy discountPolicy;

  public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
    System.out.println("memberRepository = " + memberRepository);
    System.out.println("discountPolicy = " + discountPolicy);
    this.memberRepository = memberRepository;
    this.discountPolicy = discountPolicy;
  }

  ...
```

`@Autowired`를 제외하고 실행했을 때, 정상적으로 등록이 되었다면 null이 아닌 값이 출력될 것입니다.

<img src="/assets/img/springcore/core70.png" width="70%" align="center"><br/>

`@Autowired`를 붙이고 테스트해도 같은 결과를 볼 수 있습니다.

### 수정자 주입(setter 주입)

setter라고 불리는 필드의 값을 변경하는 수정자 메서드를 통해서 의존관계를 주입하는 방법입니다. 자바 빈 프로퍼티 규약의 수정자 메서드 방식을 사용하는 방법으로, <b>선택, 변경</b> 가능성이 있는 의존관계에 사용합니다.

흔히 필드의 값을 수정할 때 관례상 다음과 같이 사용합니다.

```java
@Component
public class OrderServiceImpl implements OrderService{

  private MemberRepository memberRepository;
  private DiscountPolicy discountPolicy;

  @Autowired
  public void setMemberRepository(MemberRepository memberRepository) {
    this.memberRepository = memberRepository;
  }

  @Autowired
  public void setDiscountPolicy(DiscountPolicy discountPolicy) {
    this.discountPolicy = discountPolicy;
  }

  ...
```

`setXXX`와 같은 형식으로 이름을 작성하여 직접 수정하면 문제가 발생할 수 있기 때문에 메서드로 생성하여서 사용합니다.

사실 스프링 컨테이너는 스프링 빈을 등록하고, 연관관계를 자동으로 주입하는 2가지 라이프 사이클을 가집니다. 연관관계 주입 대상을 나타내는 것이 바로 `@Autowired`입니다.  
`@Autowired`의 기본 동작은 주입할 대상이 없으면 오류가 발생하기 때문에, 대상 없이 동작하게 하려면 `@Autowired(required = false)`로 지정해주면 됩니다.

### 필드 주입

필드 주입은 이름 그대로 필드에 값을 그대로 넣어주는 방법입니다. 코드가 간결하기 때문에 좋아보이지만 외부에서 변경이 불가능해서 테스트가 힘들고 DI 프레임워크가 없으면 아무것도 할 수 없다는 단점이 있습니다.

```java
@Component
public class OrderServiceImpl implements OrderService{

  @Autowired private MemberRepository memberRepository;
  @Autowired private DiscountPolicy discountPolicy;

  ...
```

이처럼 바로 넣어주기 때문에 private인데도 사용이 가능합니다. 테스트 코드를 돌려서 주입이 되었는지 확인해보겠습니다. 단, 이 때 기존 코드에서 return 값을 조금 수정해주겠습니다.
테스트 코드가 실행하기 전 빌드가 진행되면서 AppConfig 파일이 스캔하고 생성자 부재로 인한 컴파일 오류가 발생할 수 있기 때문입니다.

```java
@Configuration
public class AppConfig {

  ...

  @Bean
  public OrderService orderService() {
    System.out.println("call AppConfig.orderService");
    return null;
  }

  ...
```

```java
public class AutoAppConfigTest {

  @Test
  void basicScan() {
    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AutoAppConfig.class);

    MemberService memberService = ac.getBean(MemberService.class);
    assertThat(memberService).isInstanceOf(MemberService.class);

    OrderServiceImpl bean = ac.getBean(OrderServiceImpl.class);
    MemberRepository memberRepository = bean.getMemberRepository();
    System.out.println("memberRepository = " + memberRepository);
  }

}
```

<img src="/assets/img/springcore/core71.png" width="70%" align="center"><br/>

순수한 자바 테스트 코드에서는 `@Autowired`가 동작하지 않습니다. 따라서 필드 주입은 사용하지 않는 것을 권장합니다.

### 일반 메서드 주입

일반 메서드를 통해서 주입 받는 방법으로 한번에 여러 필드를 주입받을 수 있습니다. 하지만 생성자 주입이나 수정자 주입 내에서 다 처리가 되기 때문에 일반적으로 잘 사용하지 않습니다.

```java
@Component
public class OrderServiceImpl implements OrderService {
  
  private MemberRepository memberRepository;
  private DiscountPolicy discountPolicy;
  
  @Autowired
  public void init(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
    this.memberRepository = memberRepository;
    this.discountPolicy = discountPolicy;
  }

  ...

}
```

어떻게 보면 당연한 이야기지만 스프링 빈이 아닌 클래스에 `@Autowired`를 적용해도 아무런 동작을 하지 않습니다. 왜냐하면 의존관계 자동 주입은 스프링 컨테이너가 관리하는 스프링 빈이어야 동작하기 때문입니다.

---

## 옵션 처리

주입할 빈이 없어도 동작을 해야할 경우가 있습니다. 예를 들어, 스프링 빈을 옵셔널하게 해두고 등록하지 않아도 기본 로직으로 동작한다거나, 없으면 로직을 실행하지 않는 식으로 동작해야 할 때도 있습니다.  
`@Autowired`를 사용하면 <b>required</b> 옵션의 기본 값이 true이기 때문에 자동 주입 대상이 없으면 오류가 발생합니다.

자동 주입 대상을 옵션으로 처리하는 방법은 다음과 같습니다.

- `@Autowired(required = false)` : 자동 주입할 대상이 없으면 수정자 메서드 자체가 호출이 안됩니다.
- `org.springframework.lang.@Nullable` : 자동 주입할 대상이 없으면 null이 입력됩니다.
- `Optional<>` : 자동 주입할 대상이 없으면 `Optional.empty`가 입력됩니다.

각각의 경우를 테스트코드로 생성하여서 확인해보겠습니다. 

```java
package hello.core.autowired;

import hello.core.member.Member;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.lang.Nullable;

import java.util.Optional;

public class AutowiredTest {

  @Test
  void AutowiredOption() {
    ApplicationContext ac = new AnnotationConfigApplicationContext(TestBean.class);
  }

  static class TestBean {
    // 호출 안됨
    @Autowired(required = false)
    public void setNoBean1(Member member) {
      System.out.println("setNoBean1 = " + member);
    }

    // null 호출
    @Autowired
    public void setNoBean2(@Nullable Member member) {
      System.out.println("setNoBean2 = " + member);
    }

    // Optional.empty 호출
    @Autowired
    public void setNoBean3(Optional<Member> member) {
      System.out.println("setNoBean3 = " + member);
    }
  }

}
```

여기서 `Member`는 스프링 빈이 아닙니다.

<img src="/assets/img/springcore/core72.png" width="70%" align="center"><br/>

결과를 보면 'setNoBean1'이 없는데, 이것은 호출이 되지 않았기 때문입니다. 나머지 경우 호출은 되지만, 특정 값이 출력되는 것을 볼 수 있습니다.  
참고로 `@Nullable`, `Optional`은 스프링 전반에 걸쳐서 지원이 되는데, 예를 들어 생성자 자동 주입에서 특정 필드에만 사용해도 됩니다.

---

## 생성자 주입을 선택해라!

과거에는 수정자 주입과 필드 주입을 많이 사용했지만 최근 스프링을 포함한 DI 프레임워크 대부분이 생성자 주입을 권장합니다. 그 이유는 다음과 같습니다.

### 불변

대부분의 의존관계 주입은 한번 일어나면 애플리케이션 종료 시점까지 의존관계를 변경할 일이 없습니다. 오히려 대부분의 의존관계는 애플리케이션 종료 전까지 변경되면 안됩니다. (불변해야 합니다.)  
수정자 주입을 사용하면, `setXXX` 메서드를 public으로 열어두어야 합니다. 그러면 누군가가 실수로 변경할 수도 있고, 변경하면 안되는 메서드를 열어두는 것은 좋은 설계 방법이 아닙니다.  
생성자 주입은 객체를 생성할 때 단 1번만 호출이 되기 때문에 이후에 호출되는 일이 없습니다. <b>즉, 불변하게 설계할 수 있습니다.</b>

### 누락

프레임워크 없이 순수한 자바 코드를 단위 테스트하는 경우가 생각보다 많습니다. 예를 들어 단위 테스트를 하는데 다음과 같이 수정자 의존관계인 경우를 보겠습니다.

```java
@Component
public class OrderServiceImpl implements OrderService{

  private MemberRepository memberRepository;
  private DiscountPolicy discountPolicy;

  @Autowired
  public void setMemberRepository(MemberRepository memberRepository) {
    this.memberRepository = memberRepository;
  }

  @Autowired
  public void setDiscountPolicy(DiscountPolicy discountPolicy) {
    this.discountPolicy = discountPolicy;
  }

  @Override
  public Order createOrder(Long memberId, String itemName, int itemPrice) {
    Member member = memberRepository.findById(memberId);
    int discountPrice = discountPolicy.discount(member, itemPrice);

    return new Order(memberId, itemName, itemPrice, discountPrice);
  }

  ...
```

```java
package hello.core.order;

import org.junit.jupiter.api.Test;

class OrderServiceImplTest {

  @Test
  void createOrder() {
    OrderServiceImpl orderService = new OrderServiceImpl();
    orderService.createOrder(1L, "itemA", 10000);
  }

}
```
`
테스트를 실행해보면 `NullPointerException`이 발생하는 것을 볼 수 있습니다. 왜 그럴까요?  
`createOrder`만 테스트를 하고 싶다고 해도 실제로 `OrderServiceImpl`에는 `memberRepository`와 `discountPolicy`가 필요하기 때문에 임의의 값이라도 만들어서 넣어주어야 합니다. 하지만 테스트에서는 <b>누락</b>이 되었습니다. 왜 누락이 되었을까요? 테스트를 작성하는 입장에서는 의존관계에 무엇이 들어가는지 보이지 않습니다.  

생성자 주입을 사용하게 되면 테스트 실행 시 컴파일 오류가 발생하는 것을 볼 수 있습니다. 그리고 이를 통해서 누락된 것을 확인할 수 있습니다. 이제 테스트 코드가 동작하도록 수정해보겠습니다.

```java
package hello.core.order;

import hello.core.discount.FixDiscountPolicy;
import hello.core.member.Grade;
import hello.core.member.Member;
import hello.core.member.MemoryMemberRepository;
import org.junit.jupiter.api.Test;

import static org.assertj.core.api.Assertions.assertThat;

class OrderServiceImplTest {

  @Test
  void createOrder() {
    MemoryMemberRepository memberRepository = new MemoryMemberRepository();
    memberRepository.save(new Member(1L, "name", Grade.VIP));

    OrderServiceImpl orderService = new OrderServiceImpl(memberRepository, new FixDiscountPolicy());
    Order order = orderService.createOrder(1L, "itemA", 10000);
    assertThat(order.getDiscountPrice()).isEqualTo(1000);
  }

}
```

### final 키워드

생성자 주입을 사용할 때 필드에 `final` 키워드를 사용할 수 있습니다. 이를 이용해서 생성자에서만 값을 넣어줄 수 있고 외부에서는 변경을 하지 못하게 막을 수 있습니다.  
그리고 생성자를 만들 때 혹시라도 값을 설정하지 않는 경우를 막을 수 있습니다. `final`이 없으면 테스트를 실행하기 전까지는 누락된 것을 알 수가 없습니다. 하지만 `final`이 있으면 미리 오류를 볼 수 있으므로 예방할 수 있습니다.

<img src="/assets/img/springcore/core73.png" width="60%" align="center"><br/>

참고로 수정자 주입을 포함한 나머지 주입 방식은 모두 생성자 이후에 호출되므로, 필드에 `final` 키워드를 사용할 수 없습니다.

---

