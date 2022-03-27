---
layout: post
date: 2021-12-02 20:41:00
title: "스프링 핵심 원리 이해1 2부"
description: "스프링 핵심 원리 - 기본편"
subject: Spring
category: [ spring boot basic ]
tags: [ spring, basic, oop ]
use_math: true
comments: true
---

[스프링 핵심 원리 - 기본편](https://inf.run/bMZx)을 공부하고 정리하는 포스트입니다.

---

## 주문과 할인 도메인 설계

- 주문과 할인 정책
  - 회원은 상품을 주문할 수 있다.
  - 회원 등급에 따라 할인 정책을 적용할 수 있다.
  - 할인 정책은 모든 VIP는 1000원을 할인해주는 고정 금액 할인을 적용. (변경 가능)
  - 할인 정책은 변경 가능성이 높다. 최악의 경우 적용하지 않을 수도 있다. (미확정)

<img src="/assets/img/springcore/core08.png" width="70%" align="center"><br/>

1. 주문 생성 : 클라이언트는 주문 서비스에 주문 생성을 요청한다.
2. 회원 조회 : 할인을 위해서는 회원 등급이 필요하다. 그래서 주문 서비스는 회원 저장소에서 회원을 조회한다.
3. 할인 적용 : 주문 서비스는 회원 등급에 따른 할인 여부를 할인 정책에 위임한다.
4. 주문 결과 반환 : 주문 서비스는 할인 결과를 포함한 주문 결과를 반환한다.

실제로는 이러한 주문 데이터를 DB에 저장하겠지만 예제이기 때문에 생략하고 단순히 주문 결과 객체를 만들어 클라이언트에게 반환하기만 한다.

## 주문 도메인 전체

<img src="/assets/img/springcore/core09.png" width="70%" align="center"><br/>

주문 도메인 전체의 그림이다. 잘 보면 역할을 먼저 만들고 구현을 만든 것을 볼 수 있다. <b>역할과 구현을 분리</b>해서 자유롭게 구현 객체를 조립할 수 있도록 설계 했기 때문에, 나중에 다른 할인 정책을 원하면 해당 정책으로 슥 바꿔 끼우기만 하면 된다.

이제 클래스 다이어그램으로 내리면 아래와 같다.

### 주문 도메인 클래스 다이어그램

<img src="/assets/img/springcore/core10.png" width="70%" align="center"><br/>

### 주문 도메인 객체 다이어그램 1

<img src="/assets/img/springcore/core11.png" width="70%" align="center"><br/>

객체 다이어그램은 실제 `new`로 생성하여 동적으로 연관관계를 맺는 그림이라고 했었다.  
회원을 메모리에서 조회하고, 정액 할인 정책(고정금액)을 지원해도 주문 서비스를 변경하지 않아도 된다. 또한 *역할들의 협력 관계를 그대로 재사용 할 수 있다*<sup>[1](#footnote_1)</sup>.

### 주문 도메인 객체 다이어그램 2

<img src="/assets/img/springcore/core12.png" width="70%" align="center"><br/>

회원을 메모리가 아닌 실제 DB에서 조회하고, 정률 할인 정책(주문 금액에 따라 %로 할인)을 지원해도 주문 서비스를 변경하지 않아도 된다.

---

## 주문과 할인 도메인 개발

먼저 할인 정책을 만들겠다. 할인 정책 인터페이스를 만든다.

```java
package hello.core.discount;

import hello.core.member.Member;

public interface DiscountPolicy {

  /**
   *
   * @return 할인 대상 금액
   */
  int discount(Member member, int price);

}
```

```java
package hello.core.discount;

import hello.core.member.Grade;
import hello.core.member.Member;

public class FixDiscountPolicy implements DiscountPolicy{

  private int discountFixAmount = 1000; // 1000원 할인

  @Override
  public int discount(Member member, int price) {
    if(member.getGrade() == Grade.VIP) {
      return discountFixAmount;
    } else {
      return 0;
    }
  }

}
```

그리고 vip이면 1000원을 할인해주고 아니면 할인을 해주지 않는 구현체를 만들었다. 참고로 `enum`은 ==을 사용하는게 맞다.  
할인 다음은 주문을 만들 차례다. 먼저 주문 엔티티를 만들자.

```java
package hello.core.order;

public class Order {

  private Long memberId;
  private String itemName;
  private int itemPrice;
  private int discountPrice;

  public Order(Long memberId, String itemName, int itemPrice, int discountPrice) {
    this.memberId = memberId;
    this.itemName = itemName;
    this.itemPrice = itemPrice;
    this.discountPrice = discountPrice;
  }

  public int calculatePrice() {
    return itemPrice - discountPrice;
  }

  public Long getMemberId() {
    return memberId;
  }

  public void setMemberId(Long memberId) {
    this.memberId = memberId;
  }

  public String getItemName() {
    return itemName;
  }

  public void setItemName(String itemName) {
    this.itemName = itemName;
  }

  public int getItemPrice() {
    return itemPrice;
  }

  public void setItemPrice(int itemPrice) {
    this.itemPrice = itemPrice;
  }

  public int getDiscountPrice() {
    return discountPrice;
  }

  public void setDiscountPrice(int discountPrice) {
    this.discountPrice = discountPrice;
  }

  @Override
  public String toString() {
    return "Order{" +
        "memberId=" + memberId +
        ", itemName='" + itemName + '\'' +
        ", itemPrice=" + itemPrice +
        ", discountPrice=" + discountPrice +
        '}';
  }
}
```

할인된 가격을 미리 계산해주는 `calculatePrice()`도 만들었다. 그리고 출력할 때 좀 더 보기 쉽게 하기 위해 `toString()`도 추가했다.

```java
package hello.core.order;

public interface OrderService {

  Order createOrder(Long memberId, String itemName, int itemPrice);

}
```

이 인터페이스는 반환되는 주문 결과의 정보가 담긴 것이다.

```java
package hello.core.order;

import hello.core.discount.DiscountPolicy;
import hello.core.discount.FixDiscountPolicy;
import hello.core.member.Member;
import hello.core.member.MemberRepository;
import hello.core.member.MemoryMemberRepository;

public class OrderServiceImpl implements OrderService{

  private final MemberRepository memberRepository = new MemoryMemberRepository();
  private final DiscountPolicy discountPolicy = new FixDiscountPolicy();

  @Override
  public Order createOrder(Long memberId, String itemName, int itemPrice) {
    Member member = memberRepository.findById(memberId);
    int discountPrice = discountPolicy.discount(member, itemPrice);

    return new Order(memberId, itemName, itemPrice, discountPrice);
  }
}
```

이 서비스는 두 개의 객체가 필요한데, 멤버 정보와 할인 정책을 알아야 하기 때문에 `MemberRepository`와 `DiscountPolicy`를 가져왔다.

그리고 `createOrder`를 보면 알 수 있듯이, 단일 체계의 원칙을 잘 지키고 있다. OrderService의 입장에서 할인에 대한 것은 아무런 책임도 없고 그 부분은 discountPolicy가 담당하고 있다.  
만일 할인에 대한 수정이 필요하면 할인 쪽만 수정하면 된다.

---

## 주문과 할인 도메인 실행과 테스트

아래와 같이 메인 메소드를 만들고 실행하여 결과를 확인해보자.

<img src="/assets/img/springcore/core13.png" width="70%" align="center"><br/>

<img src="/assets/img/springcore/core14.png" width="70%" align="center"><br/>

우리가 원하는데로 결과가 잘 나오는 것을 확인할 수 있다. 하지만 이렇게 메인 메소드로 확인하는 것은 좋지 않다. 이제 JUnit을 사용해보자.

<img src="/assets/img/springcore/core16.png" width="70%" align="center"><br/>

<img src="/assets/img/springcore/core15.png" width="70%" align="center"><br/>

---
<a name="footnote_1">1</a> : 객체지향의 사실과 오해라는 책에서 이 문장에 대해 잘 정리되어 있다.