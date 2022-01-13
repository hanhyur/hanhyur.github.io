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

