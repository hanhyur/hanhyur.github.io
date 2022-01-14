---
layout: post
date: 2022-01-12 11:58:00
title: "스프링 컨테이너와 스프링 빈 1부"
description: "스프링 핵심 원리 - 기본편"
subject: Spring
category: [ spring ]
tags: [ spring, basic, oop ]
use_math: true
comments: true
---

[스프링 핵심 원리 - 기본편](https://inf.run/bMZx)을 공부하고 정리하는 포스트입니다.

---

# 스프링 컨테이너와 스프링 빈

## 스프링 컨테이너 생성

스프링 컨테이너가 생성되는 과정은 기본적으로 아래의 코드와 같습니다.

```java
ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
```

`new AnnotationConfigApplicationContext()` 객체를 생성하면서 AppConfig 파라미터를 넘겨주었습니다. 이렇게 하면 반환 값으로 ApplicationContext가 반환됩니다. 이 `ApplicationContext`를 스프링 컨테이너라고 보통 이야기하는데 `ApplicationContext`는 인터페이스입니다. 즉, 다형성이 적용되어 있습니다. `ApplicationContext` 인터페이스를 구현한 것 중에 하나가 `AnnotationConfigApplicationContext`인 것입니다.

`AppConfig`를 보면 Java Config로 만들어져 있습니다. 그래서 이름 그대로 '애노테이션 기반의 자바 설정을 기반으로 스프링을 만들어라'는 것입니다.

스프링 컨테이너는 XML을 기반으로 만들 수도 있고, 애노테이션 기반의 자바 설정 클래스로 만들 수도 있습니다. 최근에는 애노테이션 기반이 편리하기 때문에 대부분 이 방식으로 만듭니다. AppConfig를 만드는 방식이 바로 이 방식입니다.

참고로 스프링 컨테이너라고 표현하는데 정확히는 스프링 컨테이너를 부를 때, `BeanFactory`와 `ApplicationContext`로 구분해서 이야기합니다. `BeanFactory`를 직접 사용하는 경우는 거의 없으므로 일반적으로 `ApplicationContext`를 스프링 컨테이너라고 합니다.

### 1. 스프링 컨테이너 생성

<img src="/assets/img/springcore/core33.png" width="70%" align="center"><br/>

`new AnnotationConfigApplicationContext(AppConfig.class)`로 스프링 컨테이너를 생성하면서 구성 정보를 주었습니다. 그러면 스프링 컨테이너가 만들어지는데 컨테이너 안에는 스프링 빈 저장소라는게 있습니다. 빈 저장소에 key는 빈 이름으로 저장되고 value는 빈 객체로 저장됩니다. 

그래서 스프링 컨테이너를 생성할 때는 구성 정보를 지정해주어야 합니다. 이렇게 지정하는게 바로 AppConfig를 파라미터로 넘겨준 것입니다.

### 2. 스프링 빈 등록

스프링 컨테이너는 생성되면서 파라미터로 넘어온 설정 클래스 정보를 사용해서 스프링 빈 저장소에 빈을 등록합니다. `@Bean`이 붙은 메서드를 모두 호출해서 메서드 이름을 key로 빈 이름에 등록하고 반환하는 객체를 빈 객체로 등록합니다.

<img src="/assets/img/springcore/core34.png" width="70%" align="center"><br/>

참고로 빈의 이름은 메서드 이름을 사용하는데 임의로 부여할 수 도 있습니다.

여기서 주의해야 하는 점이 있습니다. 빈 이름은 항상 다른 이름을 부여해야 합니다. 같은 이름을 부여하면 상황에 따라 복잡한데 다른 빈이 무시되거나, 기존 빈을 덮어버리거나 설정에 따라 오류가 발생합니다. '빈 이름이 중복되면 안된다'라고 기억하도록 합시다.

과거에는 충돌나면 덮어버리거나 하는 식으로 등록이 되었는데, 최근에는 스프링 부트가 경고를 날려주고 튕겨버리도록 되어있습니다.

### 3. 스프링 빈 의존관계 설정

<img src="/assets/img/springcore/core35.png" width="70%" align="center"><br/>

스프링 컨테이너는 설정 정보를 참고하여 의존관계를 주입(DI)합니다. 동적인 객체 인스턴스 의존관계를 스프링이 연결해주는 것입니다.

스프링은 빈을 생성하고, 의존관계를 주입하는 단계가 나누어져 있습니다. 빈을 먼저 생성하고 난 다음 연결해주는데 자바 코드로 스프링 빈을 등록하면 생성자를 호출하면서 의존관계 주입도 한 번에 처리됩니다. 

---

## 컨테이너에 등록된 모든 빈 조회

컨테이너에 등록한 빈들이 제대로 등록이 되었는지 테스트 코드로 작성해서 확인을 해보겠습니다. 참고로 JUnit5부터는 public을 생략할 수 있습니다.

```java
package hello.core.beanfind;

import hello.core.AppConfig;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.config.BeanDefinition;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

class ApplicationContextInfoTest {

  AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

  @Test
  @DisplayName("모든 빈 출력하기")
  void findAllBean() {
    String[] beanDefinitionNames = ac.getBeanDefinitionNames();

    for (String beanDefinitionName : beanDefinitionNames) {
      Object bean = ac.getBean(beanDefinitionName);
      System.out.println("name = " + beanDefinitionName + " object = " + bean);
    }
  }

}
```

`ac.getBeanDefinitionNames()`을 통해서 빈에 정의된 이름을 가져올 수 있습니다. 그리고 ` ac.getBean()`을 사용하여 빈 이름으로 빈 객체(인스턴스)를 조회할 수 있습니다.

정의된 이름을 가져와서 그대로 출력해보겠습니다.

<img src="/assets/img/springcore/core36.png" width="100%" align="center"><br/>

빨간 테두리에 있는 내용들은 스프링이 내부적으로 자체 확장을 위해 사용하는 빈입니다. 초록 테두리에 appConfig가 있는데 appConfig도 스프링 빈으로 등록됩니다. 그리고 파란 테두리에 있는 빈들이 등록한 것들입니다.

하지만 이렇게 섞여있으면 불편하고 직접 짠 코드만 보고 싶으니 다음과 같은 코드를 추가하겠습니다.

```java
...

class ApplicationContextInfoTest {

  ...

  @Test
  @DisplayName("애플리케이션 빈 출력하기")
  void findApplicationBean() {
    String[] beanDefinitionNames = ac.getBeanDefinitionNames();

    for (String beanDefinitionName : beanDefinitionNames) {
      BeanDefinition beanDefinition = ac.getBeanDefinition(beanDefinitionName);

      // Role ROLE_APPLICATION : 직접 등록한 애플리케이션 빈
      // Role ROLE_INFRASTRUCTURE : 스프링이 내부에서 사용하는 빈
     if(beanDefinition.getRole() == BeanDefinition.ROLE_APPLICATION) {
        Object bean = ac.getBean(beanDefinitionName);
        System.out.println("name = " + beanDefinitionName + " object = " + bean);
      }

    }
  }

}
```

`BeanDefinition`는 빈 하나하나에 대한 메타정보이고 `beanDefinitionName`을 이용해서 하나하나 꺼낼 수 있습니다. `beanDefinition.getRole()`은 3가지가 있는데 그 중에서 주로 `BeanDefinition.ROLE_APPLICATION`이 사용됩니다. `ROLE_APPLICATION`은 개발하기 위해서 직접 등록한 빈입니다.

이렇게 작성하고 실행하면 직접 등록한 5개만 출력됩니다.

<img src="/assets/img/springcore/core37.png" width="80%" align="center"><br/>

출력 내용을 보면 각각에 맞게 등록된 것을 볼 수 있습니다. 참고로 `ROLE_INFRASTRUCTURE`는 스프링이 내부에서 사용하는 빈입니다.

---

## 스프링 빈 조회 - 기본

스프링 컨테이너에서 스프링 빈을 찾는 가장 기본적인 조회 방법은 `getBean()`이라는 메서드를 사용하는 것입니다. `getBean()`에 빈 이름과 타입을 주거나 빈 이름을 생략하고 타입만 줄 수도 있습니다.

물론 조회 대상 스프링 빈이 없으면 `NoSuchBeanDefinitionException : No bean named 'xxxx' available`이라는 예외가 발생합니다.

직접 코드를 작성하면서 확인해보겠습니다.

```java
package hello.core.beanfind;

import hello.core.AppConfig;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.NoSuchBeanDefinitionException;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.assertThrows;

class ApplicationContextBasicFindTest {

  AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

  @Test
  @DisplayName("빈 이름으로 조회")
  void findBeanByName() {
    MemberService memberService = ac.getBean("memberService", MemberService.class);

    assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
  }

  @Test
  @DisplayName("이름 없이 타입으로만 조회")
  void findBeanByType() {
    MemberService memberService = ac.getBean(MemberService.class);

    assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
  }

  @Test
  @DisplayName("구체 타입으로 조회")
  void findBeanByName2() {
    MemberService memberService = ac.getBean("memberService", MemberServiceImpl.class);

    assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
  }

  @Test
  @DisplayName("빈 이름으로 조회 X")
  void findBeanByNameX() {
    // ac.getBean("xxxx", MemberService.class)

    assertThrows(NoSuchBeanDefinitionException.class, () -> ac.getBean("xxxx", MemberService.class));
  }
}
```

테스트 코드를 하나씩 살펴보겠습니다. 먼저 빈 이름으로 조회하는 경우는 다음과 같습니다.

```java
  @Test
  @DisplayName("빈 이름으로 조회")
  void findBeanByName() {
    MemberService memberService = ac.getBean("memberService", MemberService.class);

    assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
  }
```

항상 검증은 `Assertions`로 합니다. `assertThat(memberService).isInstanceOf(MemberServiceImpl.class)`은 `memberService`의 인스턴스가 `MemberServiceImpl`인지 검증하는 코드입니다.

이름 없이 타입으로만 조회하는 경우는 다음과 같습니다.

```java
  @Test
  @DisplayName("이름 없이 타입으로만 조회")
  void findBeanByType() {
    MemberService memberService = ac.getBean(MemberService.class);

    assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
  }
```

두 경우 모두 인터페이스로 조회했기 때문에 인터페이스의 구현체가 대상이었습니다. 다음과 같이 구체 타입(구현체의 타입)으로 조회할 수 있습니다.

```java
  @Test
  @DisplayName("구체 타입으로 조회")
  void findBeanByName2() {
    MemberService memberService = ac.getBean("memberService", MemberServiceImpl.class);

    assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
  }
```

반환 타입이 `MemberService`인데 구현체로도 조회가 가능했습니다. 스프링 빈에 등록된 인스턴스 타입을 보고 결정하기 때문에 인터페이스가 아니어도 가능한 것입니다.

물론 이렇게 조회하는 것은 좋은 것은 아닙니다. 항상 역할과 구현을 구분하고 역할에 의존해야 하는데 이 코드는 구현에 의존하기 때문에 좋은 코드가 아닙니다.

마지막으로 항상 테스트는 실패 테스트를 만들어야 합니다.

```java
  @Test
  @DisplayName("빈 이름으로 조회 X")
  void findBeanByNameX() {
    // ac.getBean("xxxx", MemberService.class)

    assertThrows(NoSuchBeanDefinitionException.class, () -> ac.getBean("xxxx", MemberService.class));
  }
```

`ac.getBean("xxxx", MemberService.class)`로 조회하면 당연히 에러가 발생합니다. `NoSuchBeanDefinitionException` 에러가 발생하는데, 이 에러를 검증하기 위해서 `assertThrows`를 사용합니다. 

`assertThrows`는 오른쪽의 `() -> ac.getBean("xxxx", MemberService.class)` 코드를 실행했을 때 해당하는 예외(`NoSuchBeanDefinitionException`)가 발생해야 성공합니다.

작성한 테스트 코드를 모두 실행해보면 성공하는 것을 확인할 수 있습니다.

<img src="/assets/img/springcore/core38.png" width="70%" align="center"><br/>

---

## 스프링 빈 조회 - 동일한 타입이 둘 이상

스프링 빈을 타입으로 조회할 때 같은 타입의 스프링 빈이 둘 이상이면 오류가 발생합니다. 이 때는 빈 이름을 지정해주면 됩니다.

참고로 `ac.getBeansOfType()`을 사용하면 해당하는 타입의 모든 빈을 조회할 수 있습니다.

AppConfig를 수정할 수 없으니 편의상 테스트 코드 내부에서만 사용할 Config 클래스를 생성하겠습니다. 파라미터의 값이 다르게 들어갈 수 있기 때문에 빈의 이름이 다르고 클래스 타입이 같을 수 있습니다.

```java
...

class ApplicationContextSameBeanFindTest {

  AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(SameBeanConfig.class);

  @Configuration
  static class SameBeanConfig {

    @Bean
    public MemberRepository memberRepository1() {
      return new MemoryMemberRepository();
    }

    @Bean
    public MemberRepository memberRepository2() {
      return new MemoryMemberRepository();
    }

  }

  ...

}
```

이렇게 한 상태에서 다음과 같이 호출을 하면 에러가 발생합니다.

```java
  @Test
  @DisplayName("같은 타입 둘 이상 시 중복 오류 발생")
  void findBeanByTypeDupliacte() {
    MemberRepository bean = ac.getBean(MemberRepository.class);
  }
```

스프링 입장에서는 2개가 나오기 때문에 뭘 선택해야할지 모릅니다. 따라서 다음과 같은 에러를 볼 수 있습니다.

<img src="/assets/img/springcore/core39.png" width="100%" align="center"><br/>

따라서 다음과 같이 코드를 변경해서 검증할 수 있습니다.

```java
  @Test
  @DisplayName("같은 타입 둘 이상 시 중복 오류 발생")
  void findBeanByTypeDupliacte() {
    assertThrows(NoUniqueBeanDefinitionException.class, () -> ac.getBean(MemberRepository.class));
  }
```

빈 이름이 중복될 때 성공하는 방법으로는 빈 이름을 지정해주는 방법이 있습니다.

```java
  @Test
  @DisplayName("빈 이름을 지정하면 된다")
  void findBeanByName() {
    MemberRepository memberRepository = ac.getBean("memberRepository1", MemberRepository.class);
    assertThat(memberRepository).isInstanceOf(MemberRepository.class);
  }
```

마지막으로 중복된 것을 모두 꺼내고 싶을 때는 중복되는 특정 타입을 모두 조회하면 됩니다.

```java
  @Test
  @DisplayName("특정 타입을 모두 조회")
  void findAllBeanByType() {
    Map<String, MemberRepository> beansOfType = ac.getBeansOfType(MemberRepository.class);
    for (String key : beansOfType.keySet()) {
      System.out.println("key = " + key + " value = " + beansOfType.get(key));
    }

    System.out.println("beansOfType = " + beansOfType);
    assertThat(beansOfType.size()).isEqualTo(2);
  }
```

`ac.getBeansOfType()`로 조회하면 Map으로 나오는 것을 볼 수 있습니다. 각각의 출력과 `beansOfType` 전체를 출력하면 다음과 같이 나오는 것을 볼 수 있습니다.

<img src="/assets/img/springcore/core40.png" width="100%" align="center"><br/>

이 방법은 `Autowired`와 같이 자동으로 의존관계 주입할 때 적용되는 기능입니다.

전체 테스트 코드는 다음과 같습니다.

```java
package hello.core.beanfind;

import hello.core.member.MemberRepository;
import hello.core.member.MemoryMemberRepository;
import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.NoUniqueBeanDefinitionException;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.util.Map;

import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.assertThrows;

class ApplicationContextSameBeanFindTest {

  AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(SameBeanConfig.class);

  @Configuration
  static class SameBeanConfig {

    @Bean
    public MemberRepository memberRepository1() {
      return new MemoryMemberRepository();
    }

    @Bean
    public MemberRepository memberRepository2() {
      return new MemoryMemberRepository();
    }

  }

  @Test
  @DisplayName("같은 타입 둘 이상 시 중복 오류 발생")
  void findBeanByTypeDupliacte() {
    assertThrows(NoUniqueBeanDefinitionException.class, () -> ac.getBean(MemberRepository.class));
  }

  @Test
  @DisplayName("빈 이름을 지정하면 된다")
  void findBeanByName() {
    MemberRepository memberRepository = ac.getBean("memberRepository1", MemberRepository.class);
    assertThat(memberRepository).isInstanceOf(MemberRepository.class);
  }

  @Test
  @DisplayName("특정 타입을 모두 조회")
  void findAllBeanByType() {
    Map<String, MemberRepository> beansOfType = ac.getBeansOfType(MemberRepository.class);
    for (String key : beansOfType.keySet()) {
      System.out.println("key = " + key + " value = " + beansOfType.get(key));
    }

    System.out.println("beansOfType = " + beansOfType);
    assertThat(beansOfType.size()).isEqualTo(2);
  }

}
```