---
layout: post
date: 2022-01-31 22:04:00
title: "스프링 컨테이너와 스프링 빈 2부"
description: "스프링 핵심 원리 - 기본편"
subject: Spring
category: [ spring ]
tags: [ spring, basic, oop ]
use_math: true
comments: true
---

[스프링 핵심 원리 - 기본편](https://inf.run/bMZx)을 공부하고 정리하는 포스트입니다.

---

## 스프링 빈 조회 - 상속 관계

빈을 조회할 때 상속 관계로 되어 있어서 부모 타입으로 조회하면 어떻게 될까요? 자식 타입도 함께 조회됩니다. 이게 스프링 빈 조회의 기본 대원칙입니다.

따라서 모든 자바 객체의 부모 타입인 `Object`를 조회하면, 모든 스프링 빈이 조회됩니다.

```java
...

public class ApplicationContextExtendsFindTest {

  AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(TestConfig.class);

  @Test
  @DisplayName("부모 타입으로 조회 시 자식이 둘 이상이면 오류")
  void findBeanByParentTypeDuplicate() {
    assertThrows(NoUniqueBeanDefinitionException.class, () -> ac.getBean(DiscountPolicy.class));
  }

  @Configuration
  static class TestConfig {

    @Bean
    public DiscountPolicy rateDiscountPolicy() {
      return new RateDiscountPolicy();
    }

    @Bean
    public DiscountPolicy fixDiscountPolicy() {
      return new FixDiscountPolicy();
    }

  }

}
```

부모타입인 `DiscountPolicy`로 조회를 하면 자식인 `RateDiscountPolicy`와 `FixDiscountPolicy` 두 개 다 걸려들기 때문에 오류(NoUniqueBeanDefinitionException)가 발생합니다.

<img src="/assets/img/springcore/core41.png" width="50%" align="center"><br/>

그러면 어떻게 해야될까요? 당연히 빈 이름을 지정하면 됩니다.

```java

  @Test
  @DisplayName("자식이 둘 이상이면 빈 이름을 지정")
  void findBeanByParentTypeBeanName() {
    DiscountPolicy rateDiscountPolicy = ac.getBean("rateDiscountPolicy", DiscountPolicy.class);
    assertThat(rateDiscountPolicy).isInstanceOf(RateDiscountPolicy.class);
  }

```

`DiscountPolicy` 중에서 `RateDiscountPolicy`를 가져오기 위해서 이름을 지정해주었습니다. 타입은 부모 타입인 `DiscountPolicy`이지만 가져오는 것은 `RateDiscountPolicy`입니다.

<img src="/assets/img/springcore/core42.png" width="50%" align="center"><br/>

또한 타입으로도 조회할 수 있습니다. 물론 좋은 방법은 아닙니다.

```java

  @Test
  @DisplayName("특정 하위 타입으로 조회")
  void findBeanBySubType() {
    RateDiscountPolicy bean = ac.getBean(RateDiscountPolicy.class);
    assertThat(bean).isInstanceOf(RateDiscountPolicy.class);
  }

```

구체적인 타입 `RateDiscountPolicy`로 지정했습니다. `RateDiscountPolicy`는 하나뿐이기 때문에 당연히 성공합니다.

<img src="/assets/img/springcore/core43.png" width="50%" align="center"><br/>

그러면 왜 부모 타입으로 타입을 지정해놓았을까요? 개발이나 설계를 할 때, 역할과 구현을 나눈다고 했습니다. 구체적인 걸로 해도 괜찮지만 다른 곳에서 DI를 할 때도 한 눈에 바로 알 수 있기 때문입니다.

이번에는 부모 타입으로 모두 조회해보겠습니다.

```java

  @Test
  @DisplayName("부모 타입으로 모두 조회")
  void findBeanByParentType() {
    Map<String, DiscountPolicy> beansOfType = ac.getBeansOfType(DiscountPolicy.class);
    assertThat(beansOfType.size()).isEqualTo(2);
  }

```

<img src="/assets/img/springcore/core44.png" width="50%" align="center"><br/>

만약 `Object` 타입으로 조회를 하게 된다면 어떻게 될까요? 스프링 내부에서 사용하는 것까지 포함한 스프링 빈에 등록된 모두 다 가져오게 됩니다.

---

