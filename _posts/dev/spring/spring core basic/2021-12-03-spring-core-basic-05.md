---
layout: post
date: 2021-12-03 20:41:00
title: "스프링 핵심 원리 이해2 1부"
description: "스프링 핵심 원리 - 기본편"
subject: Spring
category: [ spring ]
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
<a name="footnote_1">1</a> : 애자일 소프트웨어 개발 선언 https://agilemanifesto.org/iso/ko/manifesto.html