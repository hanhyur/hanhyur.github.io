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

## BeanFactory와 ApplicationContext

<img src="/assets/img/springcore/core45.png" width="50%" align="center"><br/>

<b>BeanFactory</b>라는게 도대체 뭘까요? 위 그림에서 계층구조를 볼 수 있습니다. 최상위에 `BeanFactory` 인터페이스가 있고, 이 인터페이스를 상속받은 `ApplicationContext`가 있습니다. 그러면 자연스럽게 `ApplicationContext`는 `BeanFactory`에 부가기능을 더 한것이라고 이해할 수 있습니다.  
그리고 `AnnotationConfigApplicationContext`와 같은 구현 객체(클래스)가 존재합니다.

### BeanFactory

`BeanFactory`는 스프링 컨테이너의 최상위 인터페이스입니다. 그리고 스프링 빈을 관리하고 조회하는 기능이 들어있습니다. 대표적으로 제공하는 것이 `getBean()`이고, 지금까지 사용했던 대부분의 기능이 이 `BeanFactory`에서 제공하는 것입니다.

### ApplicationContext

`ApplicationContext`는 `BeanFactory`의 모든 기능을 상속받아서 제공하고 있습니다. 그러면 무슨 차이가 있길래 사용하는 것일까요?  

애플리케이션을 개발할 때는 빈을 관리하고 조회하는 기능은 물론이고 수많은 부가기능이 필요합니다.

<img src="/assets/img/springcore/core46.png" width="60%" align="center"><br/>

이처럼 `ApplicationContext`는 `BeanFactory` 뿐만 아니라, `MessageSource`, `EnvironmentCapable`, `ApplicationEventPublisher`, `ResourceLoader` 등등 몇 가지 더 가지고 있습니다.

각 기능을 잠깐 살펴보면 `MessageSource`는 국제화 기능으로, 한국에서 들어오면 한국어로, 영어권에서 들어오면 영어로 나오는 웹페이지가 있습니다. 이러한 국제화 기능을 파일로 구분해놓았다가 해당하는 파일을 제공하는 부가 기능입니다.  
`EnvironmentCapable`는 로컬, 개발, 운영 등을 구분하는, 즉 환경변수와 관련된 내용을 구분해서 처리해주는 기능입니다.  
`ApplicationEventPublisher`는 이벤트를 발행하고 구독하는 모델을 편리하게 지원해주는 기능이고, `ResourceLoader`는 파일, 클래스패스, 외부 URL 등에서 파일(리소스)을 읽어서 사용할 때 추상화해서 편리하게 쓸 수 있는 기능을 제공합니다.

정리를 해보면 `ApplicationContext`는 `BeanFactory`의 기능을 상속받아서 빈 관리기능에 더해서 편리한 부가기능을 제공합니다. 그리고 `BeanFactory`는 직접 사용할 일이 거의 없습니다.  
이러한 `BeanFactory`나 `ApplicationContext` 모두 스프링 컨테이너라고 하고, 실제로 사용하는 컨테이너는 `ApplicationContext`라고 생각하면 됩니다.

---

## 다양한 설정 형식 지원 - 자바코드, XML

자바코드로 설정하는 방법 외에 XML로 설정하는 방법도 있습니다.

<img src="/assets/img/springcore/core47.png" width="60%" align="center"><br/>

`ApplicationContext`를 구현한 것 중 하나가 `AnnotationConfigApplicationContext`인데 이것은 `AppConfig.class`를 사용했습니다. 그리고 `GenericXmlApplicationContext`라고 XML이라는 문서를 설정정보로 사용하는 것도 있습니다. 그 외에 임의로 만들어서 사용할 수도 있습니다.

최근에는 자바코드 기반의 `AnnotaionConfig`를 많이 사용하고 과거에는 XML을 많이 사용했습니다.

### Annotation 기반 자바 코드 설정 사용

`new AnnotationConfigApplicationContext(Appconfig.class)`와 같은 형태로 계속 사용했습니다. 