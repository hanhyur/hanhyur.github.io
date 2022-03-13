---
layout: post
date: 2022-03-09 23:30:00
title: "의존관계 자동 주입 2부"
description: "스프링 핵심 원리 - 기본편"
subject: Spring
category: [spring]
tags: [spring, basic, oop]
use_math: true
comments: true
---

[스프링 핵심 원리 - 기본편](https://inf.run/bMZx)을 공부하고 정리하는 포스트입니다.

---

## 롬복과 최신 트렌드

실제 개발을 해보면, 대부분 불변이고 생성자에 final 키워드를 사용하게 됩니다. 하지만 매번 생성자를 만들고, 주입 받은 값을 대입하는 코드도 만들어야 합니다.  
필드 주입처럼 좀 더 편리하게 사용하는 방법이 없을까요?

다음 코드는 기본 코드입니다.

```java
@Component
public class OrderServiceImpl implements OrderService {

  private final MemberRepository memberRepository;
  private final DiscountPolicy discountPolicy;

  @Autowired
  public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
    this.memberRepository = memberRepository;
    this.discountPolicy = discountPolicy;
  }

}
```

생성자가 1개인 경우, `@Autowired`를 생략할 수 있습니다.

```java
@Component
public class OrderServiceImpl implements OrderService {

  private final MemberRepository memberRepository;
  private final DiscountPolicy discountPolicy;

  public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
    this.memberRepository = memberRepository;
    this.discountPolicy = discountPolicy;
  }

}
```

아직 코드가 긴 거 같습니다. 여기에 <b>롬복</b> 라이브러리를 적용해보겠습니다. `Lombok`이란 Java 라이브러리로 반복되는 getter, setter, toString ..등의 반복 메서드 작성 코드를 줄여주는 코드 다이어트 라이브러리입니다.

롬복 라이브러리가 제공하는 `@RequiredArgsConstructor` 기능을 사용하면 final이 붙은 필드를 모아서 생성자를 자동으로 만들어줍니다! 따라서 아래와 같이 간결하게 만들 수 있습니다.

```java
@Component
@RequiredArgsConstructor
public class OrderServiceImpl implements OrderService {

  private final MemberRepository memberRepository;
  private final DiscountPolicy discountPolicy;

}
```

최근에는 생성자를 딱 1개 두고, `@Autowired`를 생략하는 방법을 주로 사용합니다. 여기에 Lombok 라이브러리의 `@RequiredArgsConstructor` 함께 사용하면 기능은 다 제공하면서, 코드는 깔끔하게
사용할 수 있습니다.

---

## 조회 빈이 2개 이상 - 문제

기본적으로 `@Autowired`는 타입(Type)으로 조회를 합니다. 다음과 같은 코드가 있습니다.

```java
  @Autowired
  private DiscountPolicy discountPolicy
```

`@Autowired`는 타입으로 조회하기 때문에 다음 코드와 유사하게 동작합니다. 물론 실제론 더 많은 기능을 제공합니다.

```java
  ac.getBean(DiscountPolicy.class)
```

타입으로 조회를 할 때 선택된 빈이 2개 이상이면 문제가 발생합니다.

```java
  @Component
  public class FixDiscountPolicy implements DiscountPolicy { }
```

```java
  @Component
  public class RateDiscountPolicy implements DiscountPolicy { }
```

`DiscountPolicy`의 하위 타입인 `FixDiscountPolicy`와 `RateDiscountPolicy`를 스프링 빈으로 선언했습니다. 이 상태에서 의존관계 자동 주입을 실행하게 되면 `NoUniqueBeanDefinitionException` 오류가 발생하는 것을 볼 수 있습니다.  
왜냐하면 타입으로 조회를 한다고 했는데, `FixDiscountPolicy`와 `RateDiscountPolicy`의 타입이 `DiscountPolicy`로 동일하기 때문입니다. 오류 메시지에 아주 친절하게 '하나의 빈을 기대했는데, `fixDiscountPolicy`, `rateDiscountPolicy` 2개가 발견되었다'라고 알려줍니다.

이 때 하위타입으로 지정할 수도 있지만, 하위 타입으로 지정하는 것은 DIP(의존관계 역전 원칙)을 위배하고 유연성이 떨어집니다. 그리고 이름만 다르고, 완전히 동일한 타입의 스프링 빈이 2개 있을 수 있기 때문에 해결이 되지 않습니다.  
스프링 빈을 수동 등록해서 문제를 해결해도 되지만, 의존관계 자동 주입에서 해결하는 여러가지 방법이 있습니다.

---

## @Autowired 필드 명, @Qualifier, @Primary

여러 개의 빈이 선택될 때 해결 방법을 하나씩 알아보겠습니다. 조회 빈이 2개 이상일 때 해결방법은 아래 세 가지가 있습니다.

- `@Autowired` 필드 명 매칭
- `@Qualifier` => `@Qualifier`끼리 매칭 => 빈 이름 매칭
- `@Primary` 사용

### @Autowired 필드 명 매칭

`@Autowired`는 타입으로 조회를 한다고 했습니다. 타입 매칭을 시도할 때 여러 빈이 있다면 필드 이름(파라미터 이름)으로 빈 이름을 추가 매칭합니다.  

```java
  @Autowired
  private DiscountPolicy rateDiscountPolicy
```

필드 명이 `rateDiscountPolicy`이므로 똑같이 하면 정상적으로 주입이 됩니다. 이처럼 필드 명 매칭은 타입 매칭이 먼저 시도되고, 그 결과에 여러 빈이 있을 때 추가로 동작하는 기능입니다.

정리하면 타입 매칭이 일어나고, 타입 매칭 결과가 2개 이상인 경우 필드 명(파라미터 명)으로 빈 이름을 매칭합니다.

### @Qualifier 사용

`@Qualifier`는 추가 구분자를 붙여주는 방법입니다. 주입 시 추가적인 방법을 제공하는 것이지 빈 이름을 변경하는 것은 아닙니다.

```java
  @Component
  @Qualifier("mainDiscountPolicy")
  public class RateDiscountPolicy implements DiscountPolicy { }
```

```java
  @Component
  @Qualifier("mainDiscountPolicy")
  public class FixDiscountPolicy implements DiscountPolicy { }
```

주로 사용할 `RateDiscountPolicy`에 `@Qualifier("mainDiscountPolicy")`를 주고, `FixDiscountPolicy`는 이름을 그대로 사용했습니다. 이렇게 이름을 지정해준 다음 아래와 같이 사용할 수 있습니다.

```java
  @Autowired
  public OrderServiceImpl(MemberRepository memberRepository, @Qualifier("mainDiscountPolicy") DiscountPolicy discountPolicy) {
    this.memberRepository = memberRepository;
    this.discountPolicy = discountPolicy;
  }
```

이처럼 생성자 자동 주입 시 `@Qualifier`를 추가하여 찾아서 주입할 수 있습니다. 또한 수정자 주입 시에도 사용할 수 있습니다.

```java
  @Autowired
  public DiscountPolicy setDiscountPolicy(@Qualifier("mainDiscountPolicy") DiscountPolicy discountPolicy) {
    return discountPolicy;
  }
```

만약 `@Qualifier`를 주입할 때 `@Qualifier("mainDiscountPolicy")`를 못찾으면 어떻게 될까요? 이 경우 `mainDiscountPolicy`라는 이름의 스프링 빈을 찾습니다.  
물론 `@Qualifier`는 `@Qualifier`를 찾는 용도로만 사용하는게 명확하고 좋습니다.

정리하면, `@Qualifier`는 `@Qualifier`끼리 매칭을 먼저 합니다. 그리고 없으면 해당하는 빈 이름을 매칭하고 찾지 못하면 `NoSuchBeanDefinitionException` 예외가 발생합니다.

### @Primary 사용

우선순위를 지정하는 방법으로 `@Autowired`시에 여러 빈이 매칭되면 `@Primary`가 우선권을 가집니다.

`rateDiscountPolicy`가 우선권을 가지도록 해보겠습니다.

```java
  @Component
  @Primary
  public class RateDiscountPolicy implements DiscountPolicy { }

  @Component
  public class FixDiscountPolicy implements DiscountPolicy { }
```

이처럼 `@Qualifier`에 비해 `@Primary`가 훨씬 간편하고 사용하기 편합니다.

### @Primary, @Qualifier 활용

코드에서 자주 사용하는 메인 데이터베이스의 커넥션을 획득하는 스프링 빈이 있고, 코드에서 특별한 기능으로 가끔 사용하는 서브 데이터베이스의 커넥션을 획득하는 스프링 빈이 있다고 생각해보겠습니다.  
메인 데이터베이스의 커넥션을 획득하는 스프링 빈은 `@Primary`를 적용해서 조회하는 곳에서 `@Qualifier` 지정 없이 편리하게 조회하고, 서브 데이터베이스 커넥션 빈을 획득할 때는 `@Qualifier`를 지정해서
명시적으로 획득 하는 방식으로 사용하면 코드를 깔끔하게 유지할 수 있습니다. 물론 이 때 메인 데이터베이스의 스프링 빈을 등록할 때 `@Qualifier`를 지정해주는 것은 상관없습니다.

그렇다면 `@Primary`는 기본 값처럼 동작하는 것이고, `@Qualifier`는 매우 상세하게 동작하는 것으로 볼 수 있습니다. 이런 경우 어떤 것이 우선권을 가져갈까요? 스프링은 자동보다는 수동이, 넒은 범위의 선택권 보다는 좁은 범위의 선택권이 우선 순위가 높습니다. 따라서 여기서도 `@Qualifier`가 우선권이 높습니다.

---

## 애노테이션 직접 만들기

실무에서 종종 애노테이션을 직접 만들기도 합니다. `@Qualifier`를 사용할 때 `@Qualifier("mainDiscountPolicy")`와 같이 <b>문자</b>를 사용하면 컴파일 시 문제가 있습니다. 문자는 컴파일 시 체크가 되지 않습니다.  
이를 해결하기 위해 다음과 같이 애노테이션을 만들 수 있습니다.

```java
  package hello.core.annotation;

  import org.springframework.beans.factory.annotation.Qualifier;

  import java.lang.annotation.*;

  @Target({ElementType.FIELD, ElementType.METHOD, ElementType.PARAMETER, ElementType.TYPE, ElementType.ANNOTATION_TYPE})
  @Retention(RetentionPolicy.RUNTIME)
  @Inherited
  @Documented
  @Qualifier("mainDiscountPolicy")
  public @interface MainDiscountPolicy {
  }
```

그리고 생성한 애노테이션은 사용할 위치에 추가해주면 됩니다.

```java
  @Component
  @MainDiscountPolicy
  public class RateDiscountPolicy implements DiscountPolicy { }
```

```java
  @Autowired
  public OrderServiceImpl(MemberRepository memberRepository, @MainDiscountPolicy DiscountPolicy discountPolicy) {
    this.memberRepository = memberRepository;
    this.discountPolicy = discountPolicy;
  }
```

물론 `@Primary`를 잘 사용할 수 있다면 `@Primary`를 사용하는 것이 더 쉬울 수 있습니다.

애노테이션에는 <b>상속</b>이라는 개념이 없습니다. 이렇게 여러 애노테이션을 모아서 사용하는 기능은 스프링이 지원해주는 기능이므로, `@Qualifier` 뿐만 아니라 다른 애노테이션들도 함께 조합해서 사용할 수 있습니다. 심지어 `@Autowired`도 재정의할 수 있습니다.  
하지만 스프링이 제공하는 기능을 뚜렷한 목적없이 무분별하게 재정의하는 것은 유지보수에 큰 혼란을 야기할 수 있기 때문에 자제하는 것이 좋습니다.

---

## 조회한 빈이 모두 필요할 떄, List, Map

의도적으로 해당 타입의 스프링 빈이 다 필요한 경우가 있습니다. 예를 들어 할인과 관련된 서비스를 제공하는데 클라이언트가 할인의 종류를 선택할 수 있다고 생각해보겠습니다. 이 때, 스프링을 사용하면 소위 말하는 전략 패턴을 쉽게 구현할 수 있습니다.

```java
package hello.core.autowired;

import hello.core.discount.DiscountPolicy;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import java.util.List;
import java.util.Map;

public class AllBeanTest {

  @Test
  void findAllBean() {
    ApplicationContext ac = new AnnotationConfigApplicationContext(DiscountService.class);
  }

  static class DiscountService {
    private final Map<String, DiscountPolicy> policyMap;
    private final List<DiscountPolicy> policyList;

    @Autowired
    public DiscountService(Map<String, DiscountPolicy> policyMap, List<DiscountPolicy> policyList) {
      this.policyMap = policyMap;
      this.policyList = policyList;
      System.out.println("policyMap = " + policyMap);
      System.out.println("policyList = " + policyList);
    }
  }

}
```

먼저 테스트 코드를 작성했습니다. 위와 같이 생성한 후 출력해보면, 당연히 아무 값도 없습니다.

<img src="/assets/img/springcore/core74.png" width="60%" align="center"><br/>

왜냐하면 `DiscountService`만 스프링 빈으로 등록되었기 때문입니다.

이전에 사용하던 것들도 사용하기 위해서 `AutoAppConfig.class`를 추가해줍니다. 그러면, 기존에 존재하던 `rateDiscountPolicy`와 `fixDiscountPolicy`가 빈으로 등록됩니다.

```java
  public class AllBeanTest {

    @Test
    void findAllBean() {
      ApplicationContext ac = new AnnotationConfigApplicationContext(AutoAppConfig.class, DiscountService.class);
    }

    ...
    
```

<img src="/assets/img/springcore/core75.png" width="60%" align="center"><br/>

이전과 달리 출력 결과가 나온 것을 볼 수 있습니다.

여기까지는 아 그렇네..하고 넘어갈 수 있지만, 여기서 비즈니스 로직을 추가해보겠습니다.