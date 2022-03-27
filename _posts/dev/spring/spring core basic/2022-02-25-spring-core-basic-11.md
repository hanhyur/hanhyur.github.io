---
layout: post
date: 2022-02-25 19:00:00
title: "컴포넌트 스캔"
description: "스프링 핵심 원리 - 기본편"
subject: Spring
category: [ spring core basic ]
tags: [ spring, basic, oop ]
use_math: true
comments: true
---

[스프링 핵심 원리 - 기본편](https://inf.run/bMZx)을 공부하고 정리하는 포스트입니다.

---

# 컴포넌트 스캔

컴포넌트 스캔이랑 의존관계 자동 주입은 서로 연관 관계가 있습니다. 이 둘의 연관관계를 먼저 알아보겠습니다.

## 컴포넌트 스캔과 의존관계 자동 주입 시작하기

지금까지 스프링 빈을 등록할 때는 자바 코드의 `@Bean`이나 XML의 `<bean>` 등을 통해서 설정 정보에 직접 등록할 스프링 빈을 나열했습니다.  
간단한 예제에서는 개수가 적어서 괜찮았지만, 실무에서는 무수히 많은 스프링 빈을 일일히 등록하기 힘들고, 설정 정보가 커지고, 혹은 누락되는 등의 문제가 생길 수 있습니다.

그래서 스프링은 설정 정보가 없어도 자동으로 스프링 빈을 등록하는 '컴포넌트 스캔'이라는 기능을 제공합니다. 또 의존관계도 자동으로 주입하는 `@Autowired`라는 기능도 제공합니다.

직접 코드를 통해서 알아보기 위해, `AutoAppConfig.java`를 만들어줍니다.

```java
package hello.core;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.FilterType;

@Configuration
@ComponentScan(
    excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class)
)
public class AutoAppConfig {

}
```

`@ComponentScan`은 `@Component` 애노테이션이 붙은 클래스를 찾아서 자동으로 등록해주는데 기존의 직접 등록했던 `AppConfig.class`에 있는 것을 등록하는 것을 방지하기 위해서 필터를 추가했습니다.

컴포넌트 스캔을 사용하기 위해서는 `@ComponentScan`을 붙여주면 됩니다. 수동으로 등록해주던 것과 다르게 `@Bean`으로 등록한 클래스가 없습니다.

컴포넌트 스캔은 이름 그대로 `@Component` 애노테이션이 붙은 클래스를 스캔해서 스프링 빈으로 등록합니다. `@Configuration`의 내부를 살펴보면, `@Component`가 들어있는 것을 확인할 수 있습니다.

사용할 클래스들에 `@Component`를 추가해주겠습니다.

```java
@Component
public class MemoryMemberRepository implements MemberRepository {

    ...

}
```

```java
@Component
public class RateDiscountPolicy implements DiscountPolicy {

    ...

}
```

```java
@Component
public class MemberServiceImpl implements MemberService {

    ...

}
```

여기서 끝나는게 아니라 한 가지 더 해주어야 합니다.  
`@Component`는 직접 등록하는게 아니라 자동으로 등록해주는 것입니다. 코드를 살펴보니 어라? 그러면 의존 관계 주입은 어떻게 할까요?

기존의 AppConfig는 의존관계 주입을 명시해놓았습니다. 하지만 AutoAppConfig는 아무것도 없습니다. 그냥 클래스가 스프링 빈으로 등록됩니다.

그래서 사용하는 것이 `@Autowired`라는 것입니다. `@Autowired`를 생성자에 붙여주면 스프링이 타입에 맞는 것을 찾아와서 의존관계 주입을 자동으로 연결해줍니다.

`@ComponentScan`을 쓰면 빈이 자동으로 등록되지만 의존관계를 설정할 수 있는 방법이 없기 때문에 `@Autowired`를 쓰게됩니다.

```java
@Component
public class MemberServiceImpl implements MemberService {

    private final MemberRepository memberRepository;

    @Autowired // ac.getBean(MemberRepository.class)
    public MemberServiceImpl(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }

    ...

}
```

OrderServiceImpl에도 추가해주겠습니다.

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

}
```

이제 테스트 코드를 작성해서 확인해보겠습니다.

```java
public class AutoAppConfigTest {

  @Test
  void basicScan() {
    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AutoAppConfig.class);

    MemberService memberService = ac.getBean(MemberService.class);
    assertThat(memberService).isInstanceOf(MemberService.class);
  }

}
```

기존에는 'AppConfig.class'를 사용했지만 이제는 'AutoAppConfig.class'를 사용합니다. `getBean()`을 사용해서 잘 조회되는지 확인해보면 정상적으로 동작하는 것을 볼 수 있습니다.

`AnnotationConfigApplicationContext`을 사용하는 것은 동일하지만 설정 정보를 다른 클래스로 넘겨줍니다.  
로그를 잘 읽어보면 컴포넌트 스캔이 동작하는 것을 볼 수 있습니다.

<img src="/assets/img/springcore/core63.png" width="50%" align="center"><br/>

### ComponentScan

<img src="/assets/img/springcore/core64.png" width="50%" align="center"><br/>

`@ComponentScan`은 `@Component`가 붙은 모든 클래스를 스프링 빈으로 컨테이너에 등록합니다. 이 때 빈 이름을 주의해야 합니다. 스프링 빈의 기본 이름은 클래스명을 사용하되 맨 앞글자만 소문자를 사용합니다.

예를 들어 'MemberServiceImpl' 클래스는 'memberServiceImpl'과 같은 형식으로 작성해야 합니다. 빈의 이름을 직접 지정하고 싶으면 `@Component("memberService2")`와 같이 할 수 있습니다.

### Autowired 의존관계 자동 주입

<img src="/assets/img/springcore/core65.png" width="50%" align="center"><br/>

생성자에 `@Autowired`를 지정하면 스프링 컨테이너가 자동으로 타입이 같은 빈을 찾아서 주입해줍니다.

<img src="/assets/img/springcore/core66.png" width="50%" align="center"><br/>

생성자에 파라미터가 많아도 다 찾아서 주입해줍니다.

---

## 탐색 위치와 기본 스캔 대상

모든 자바 클래스를 컴포넌트 스캔하려면 엄청난 시간이 걸립니다. 그래서 꼭 필요한 위치부터 탐색하도록 시작 위치를 지정해 줄 수 있습니다.

```java
@ComponentScan(
    basePackages = "hello.core"
)
```

만일 "hello.core.member"로 지정하면 member 패키지부터 하위 패키지를 컴포넌트 스캔하기 때문에 member만 스캔의 대상이 됩니다.

`basePackagesClasses`로 지정한 클래스의 패키지를 탐색 시작 위치로 지정할 수도 있습니다.  
지정하지 않으면 `@ComponentScan`이 붙은 클래스의 패키지가 시작 위치가 됩니다.

프로젝트 메인 설정 정보는 프로젝트를 대표하는 정보이기 때문에 프로젝트 시작 루트 위치에 두는 것이 좋다고 생각합니다.

최근 스프링 부트는 설정 정보 클래스의 위치를 프로젝트 최상단에 두는 방법을 기본적으로 사용합니다.  
스프링 부트의 대표 시작 정보인 `@SpringBootApplication`을 프로젝트 시작 루트 위치에 두는 것이 관례이고 이 설정안에 `@ComponentScan`이 들어있습니다.

### 컴포넌트 스캔 기본 대상

컴포넌트 스캔에는 `@Component` 뿐만 아니라 다음과 같은 내용들도 추가로 대상에 포함합니다.

- @Component : 컴포넌트 스캔에서 사용
- @Controller : 스프링 MVC 컨트롤러에서 사용
- @Service : 스프링 비즈니스 로직에서 사용
- @Repository : 스프링 데이터 접근 계층에서 사용
- @Configuration : 스프링 설정 정보에서 사용

각 클래스들의 소스 코드를 보면 `@Component`가 포함되어 있습니다.

사실 애노테이션에는 상속관계라는 것이 없습니다. 그래서 애노테이션이 특정 애노테이션을 인식할 수 있는 것은 자바가 아닌 스프링이 지원하는 기능입니다.

또한 컴포넌트 스캔의 용도 뿐만 아니라 다음 애노테이션이 있으면 스프링은 부가 기능을 수행합니다.

- @Controller : 스프링 MVC 컨트롤러로 인식
- @Repository : 스프링 데이터 접근 계층으로 인식하고, 데이터 계층의 예외를 스프링 예외로 변환해줍니다.
- @Configuration : 앞서 보았듯이 스프링 설정 정보로 인식하고, 스프링 빈이 싱글톤을 유지하도록 추가
처리를 합니다.
- @Service : 사실 @Service는 특별한 처리를 하지 않습니다. 대신 개발자들이 '핵심 비즈니스 로직이 여기에 있겠구나'라고 비즈니스 계층을 인식하는데 도움이 됩니다.

---

## 필터

필터에는 컴포넌트 스캔 대상을 추가로 지정하는 `includeFilters`와 컴포넌트 스캔에서 제외할 대상을 지정하는 `excludeFilters`가 있습니다.

먼저 컴포넌트 스캔 대상에 추가할 애노테이션과 제외할 애노테이션을 만듭니다.

```java
package hello.core.scan.filter;

import java.lang.annotation.*;

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyIncludeComponent {
}
```

```java
package hello.core.scan.filter;

import java.lang.annotation.*;

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyExcludeComponent {
}
```

이어서 클래스도 간단하게 만들어줍니다.

```java
package hello.core.scan.filter;

@MyIncludeComponent
public class BeanA {
}
```

```java
package hello.core.scan.filter;

@MyExcludeComponent
public class BeanB {
}
```

각각의 클래스는 포함하는 애노테이션 `@MyIncludeComponent`와 제외하는 애노테이션 `@MyExcludeComponent`가 적용되어 있습니다.

이제 테스트코드를 생성하여 테스트를 해보겠습니다.

```java
package hello.core.scan.filter;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.NoSuchBeanDefinitionException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.FilterType;

import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.assertThrows;
import static org.springframework.context.annotation.ComponentScan.*;

public class ComponentFilterAppConfigTest {

  @Test
  void filterScan() {
    ApplicationContext ac = new AnnotationConfigApplicationContext(ComponentFilterAppConfig.class);
    BeanA beanA = ac.getBean("beanA", BeanA.class);
    assertThat(beanA).isNotNull();

    assertThrows(NoSuchBeanDefinitionException.class, () -> ac.getBean("beanB", BeanB.class));
  }

  @Configuration
  @ComponentScan(
      includeFilters = @Filter(type = FilterType.ANNOTATION, classes = MyIncludeComponent.class),
      excludeFilters = @Filter(type = FilterType.ANNOTATION, classes = MyExcludeComponent.class)
  )
  static class ComponentFilterAppConfig {
  }
}
```

`includeFilters`에 `@MyIncludeComponent` 애노테이션을 추가해서 BeanA는 스프링 빈에 등록이 되지만,
`excludeFilters`에 `@MyExcludeComponent` 애노테이션이 추가된 BeanB는 스프링 빈에 등록되지 않습니다.

이렇게 FilterType은 5가지 옵션이 있습니다.

- ANNOTATION : 기본값, 애노테이션을 인식해서 동작합니다.  
ex) `org.example.SomeAnnotation`
- ASSIGNABLE_TYPE : 지정한 타입과 자식 타입을 인식해서 동작합니다.  
ex) `org.example.SomeClass`
- ASPECTJ : AspectJ 패턴에 사용합니다.  
ex) `org.example..*Service+`
- REGEX : 정규 표현식입니다.  
ex) `org\.example\.Default.*`
- CUSTOM : `TypeFilter`이라는 인터페이스를 구현해서 처리합니다.  
ex) `org.example.MyTypeFilter`

만약, BeanA도 제외하고 싶다면 다음과 같이 추가할 수 있습니다.

```java
  @Configuration
  @ComponentScan(
      includeFilters = @Filter(type = FilterType.ANNOTATION, classes = MyIncludeComponent.class),
      excludeFilters = @Filter(type = FilterType.ANNOTATION, classes = MyExcludeComponent.class),
      @Filter(type = FilterType.ASSIGNABLE_TYPE, classes = BeanA.class)
  )
  static class ComponentFilterAppConfig {
  }
```

사실 `@Component`면 충분하기 때문에, `includeFilters`를 사용할 일은 거의 없습니다. `excludeFilters`는 여러가지 이유때문에 간혹 사용할 때가 있습니다.

최근 스프링 부트는 컴포넌트 스캔을 기본으로 제공하는데, 옵션을 변경하면서 사용하기 보다는 디폴트에 최대한 맞춰서 사용하는 것이 좋습니다.

---

## 중복 등록과 충돌

컴포넌트 스캔에서 같은 이름의 빈을 등록하면 어떻게 될까요? 다음 두 가지 상황은 생각해보겠습니다.

1. 자동 빈 등록 vs 자동 빈 등록
2. 수동 빈 등록 vs 자동 빈 등록

### 자동 빈 등록 vs 자동 빈 등록

다음과 같이 `MemberServiceImpl`과 `OrderServiceImpl`의 빈 이름을 동일하게 설정해보겠습니다.

```java
@Component("Service")
public class MemberServiceImpl implements MemberService {
    ...
```

```java
@Component("Service")
public class OrderServiceImpl implements OrderService {
    ...
```

그리고 자동으로 등록해주는 테스트 코드 `AutoAppConfigTest`를 실행시키면 다음과 같이 `ConflictingBeanDefinitionException` 예외가 발생한 것을 볼 수 있습니다.

<img src="/assets/img/springcore/core67.png" width="70%" align="center"><br/>

### 수동 빈 등록 vs 자동 빈 등록

빈이 자동 등록될 때 클래스 명에서 맨 앞글자를 소문자로 바꾼 이름으로 등록된다고 했습니다. 그렇다면 그것과 같은 이름을 가진 빈을 수동으로 추가해주고 어떻게 되는지 확인해보겠습니다.

```java
@Component
public class MemoryMemberRepository implements MemberRepository {
    ...
```

```java
public class AutoAppConfig {

  @Bean(name = "memoryMemberRepository")
  MemberRepository memberRepository() {
    return new MemoryMemberRepository();
  }
  
}
```

<img src="/assets/img/springcore/core68.png" width="70%" align="center"><br/>

실행을 해보니 예상과 다르게 성공한 것을 볼 수 있습니다. 성공한 로그를 잘 살펴보면 오버라이딩이라는 단어를 볼 수 있습니다.  
이 경우 수동 빈 등록이 우선권을 가지고 수동 빈이 자동 빈을 오버라이딩 해버립니다.

여기서 한 가지 생각해보아야 할 것이 있습니다. 개발자가 이러한 결과를 기대하고 의도적으로 만들었다면, 자동보다 수동이 우선권을 가지는 것이 좋습니다. 하지만 현실에서는 개발자가 의도적으로 이러한 결과를 만들기보다는 여러 설정이 꼬여서 만들어지는 경우가 대부분입니다.  
이렇게 되면 정말 찾기 어려운 버그, 잡기 어려운 애매한 버그가 되버립니다.

최근 스프링 부트에서는 수동 빈 등록과 자동 빈 등록이 충돌하면 오류가 발생하도록 기본 값을 바꾸었습니다.  
스프링 부트인 `CoreApplication`을 실행해보면 다음과 같은 오류를 볼 수 있습니다.

<img src="/assets/img/springcore/core69.png" width="70%" align="center"><br/>

`spring.main.allow-bean-definition-overriding`의 기본 값이 false로 되어있습니다. 이 옵션을 true로 바꾸면 overriding이 되어서 실행이 가능합니다.