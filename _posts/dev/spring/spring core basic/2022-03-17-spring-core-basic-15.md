---
layout: post
date: 2022-03-17 21:48:00
title: "빈 스코프 1부"
description: "스프링 핵심 원리 - 기본편"
subject: Spring
category: [ spring core basic ]
tags: [ spring, basic, oop ]
use_math: true
comments: true
---

[스프링 핵심 원리 - 기본편](https://inf.run/bMZx)을 공부하고 정리하는 포스트입니다.

---

# 빈 스코프

지금까지 우리는 스프링 빈이 스프링 컨테이너의 시작과 함께 생성되어서 스프링 컨테이너가 종료될 때까지 유지된다고 학습했습니다. 이것은 스프링 빈이 기본적으로 싱글톤 스코프로 생성되기 때문입니다.  

빈 스코프는 말 그대로 빈이 존재할 수 있는 범위를 뜻합니다. 스프링은 다음과 같은 다양한 스코프를 지원합니다.

- 싱글톤 : 기본 스코프. 스프링 컨테이너의 시작과 종료까지 유지되는 가장 넓은 범위의 스코프입니다.
- 프로토타입 : 스프링 컨테이너는 프로토타입의 빈 생성과 의존관계 주입까지만 관여하고 그 이상 관여하지 않는 매우 짧은 범위의 스코프입니다.
- 웹 관련 스코프
  - request : 웹 요청이 들어오고 나갈 때까지 유지되는 스코프.
  - session : 웹 세션이 생성되고 종료될 때까지 유지되는 스코프.
  - application : 웹의 서블릿 컨텍스트와 같은 범위로 유지되는 스코프.

빈 스코프는 다음과 같이 지정할 수 있습니다. 먼저 컴포넌트 스캔 자동 등록은 아래와 같습니다.

```java
@Scope("prototype")
@Component
public class HelloBean { }
```

수동 등록은 다음과 같습니다.

```java
@Scope("prototype")
@Bean
PrototypeBean HelloBean() {
  return new HelloBean();
}
```

---

## 프로토타입 스코프

기본적으로 스프링 빈은 싱글톤 스코프입니다. <b>싱글톤 스코프의 빈을 조회하면 스프링 컨테이너는 항상 같은 인스턴스의 스프링 빈을 반환합니다.</b>  
반면에 프로토타입 스코프를 스프링 컨테이너에서 조회하면 스프링 컨테이너는 항상 새로운 인스턴스를 생성해서 반환합니다.

<img src="/assets/img/springcore/core80.png" width="60%" align="center"><br/>

싱글톤 빈 요청은 위 그림과 같이 동작합니다. 클라이언트 A, B, C가 스프링 컨테이너에 요청을 하면 항상 같은 빈이 반환되는 것을 볼 수 있습니다.

프로토타입의 경우 어떻게 되는지 보겠습니다.

<img src="/assets/img/springcore/core81.png" width="60%" align="center"><br/>

<img src="/assets/img/springcore/core82.png" width="60%" align="center"><br/>

먼저 프로토타입 스코프의 빈을 스프링 컨테이너에 요청(동시에 하든, 순차로 하든)하면, 스프링 컨테이너는 요청된 이 시점에 프로토타입 빈을 생성하고, 필요한 의존관계를 주입합니다. 그리고 스프링 컨테이너는 생성한 프로토타입 빈을 클라이언트에 반환해줍니다. 그리고 버립니다. 더 이상 컨테이너에서 관리하지 않습니다. 따라서 같은 요청이 오면 매번 새로운 프로토타입 빈을 생성해서 반환합니다.

핵심은 <b>스프링 컨테이너는 프로토타입 빈을 생성하고, 의존관계를 주입한 후 초기화까지만 처리한다</b>는 것입니다. 클라이언트에 빈을 반환한 이후 스프링 컨테이너는 생성된 프로토타입을 관리하지 않습니다.  
프로토타입 빈을 관리할 책임은 프로토타입 빈을 받은 클라이언트에게 있습니다. 그래서 `@PreDestroy`와 같은 종료 메서드가 호출되지 않습니다.

먼저, 싱글톤 빈을 확인해보겠습니다.

```java
package hello.core.scope;

import org.junit.jupiter.api.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Scope;

import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;

import static org.assertj.core.api.Assertions.assertThat;

public class SingletonTest {

  @Test
  void singletonBeanFind() {
    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(SingletonBean.class);

    SingletonBean singletonBean1 = ac.getBean(SingletonBean.class);
    SingletonBean singletonBean2 = ac.getBean(SingletonBean.class);
    System.out.println("singletonBean1 = " + singletonBean1);
    System.out.println("singletonBean2 = " + singletonBean2);
    assertThat(singletonBean1).isSameAs(singletonBean2);
  }

  @Scope("singleton")
  static class SingletonBean {

    @PostConstruct
    public void init() {
      System.out.println("SingletonBean.init");
    }

    @PreDestroy
    public void destroy() {
      System.out.println("SingletonBean.destroy");
    }

  }
}
```

<img src="/assets/img/springcore/core83.png" width="60%" align="center"><br/>

같은 빈이 반환된 것을 볼 수 있습니다. 이제 프로토타입 빈도 확인해보겠습니다.

```java
package hello.core.scope;

import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Scope;

import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;

import static org.assertj.core.api.Assertions.assertThat;

public class PrototypeTest {

  @Test
  void prototypeBeanFind() {
    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(PrototypeBean.class);
    System.out.println("find prototypeBean1");
    PrototypeBean prototypeBean1 = ac.getBean(PrototypeBean.class);
    System.out.println("find prototypeBean2");
    PrototypeBean prototypeBean2 = ac.getBean(PrototypeBean.class);
    System.out.println("prototypeBean1 = " + prototypeBean1);
    System.out.println("prototypeBean2 = " + prototypeBean2);
    assertThat(prototypeBean1).isNotSameAs(prototypeBean2);
  }

  @Scope("prototype")
  static class PrototypeBean {

    @PostConstruct
    public void init() {
      System.out.println("PrototypeBean.init");
    }

    @PreDestroy
    public void destroy() {
      System.out.println("PrototypeBean.destroy");
    }

  }
}
```

<img src="/assets/img/springcore/core84.png" width="60%" align="center"><br/>

`find prototypeBean1`을 한 다음에 `PrototypeBean.init`이 호출되었습니다. 즉, 이 때 생성된 것입니다. 이렇게 생성된 2개의 빈을 보면 값이 다릅니다. 그리고 `ac.close()`로 스프링 컨테이너를 종료했지만 `destroy`는 보이지 않습니다.

싱글톤 빈은 스프링 컨테이너 생성 시점에 초기화 메서드가 실행 되지만, 프로토타입 스코프의 빈은 스프링 컨테이너에서 빈을 조회할 때 생성되고, 초기화 메서드도 실행됩니다.  
프로토타입 빈을 2번 조회했으므로 완전히 다른 스프링 빈이 생성되었고, 초기화도 2번 실행된 것을 확인할 수 있습니다.  
싱글톤 빈은 스프링 컨테이너가 관리하기 때문에 스프링 컨테이너가 종료될 때 빈의 종료 메서드가 실행되지만, 프로토타입 빈은 스프링 컨테이너가 생성과 의존관계 주입 그리고 초기화까지만 관여하고 더 이상 관리하지 않습니다. 따라서 종료 메서드가 실행되지 않습니다.

### 정리

프로토타입 빈의 특징을 정리하면 다음과 같습니다.

- 스프링 컨테이너에 요청할 때 마다 새롭게 생성된다.
- 스프링 컨테이너는 프로토타입 빈의 생성과 의존관계 주입 그리고 초기화까지만 관여한다.
- 종료 메서드가 호출되지 않는다.
- 프로토타입 빈은 프로토타입 빈을 조회한 클라이언트가 관리해야 한다. 종료 메서드에 대한 호출도 클라이언트가 직접해야 한다.

---

## 프로토타입 스코프 - 싱글톤 빈과 함께 사용시 문제점

스프링 컨테이너에 프로토타입 스코프의 빈을 요청하면 항상 새로운 객체 인스턴스를 생성해서 반환해준다고 했습니다. 여기서 문제가 있습니다. 싱글톤 빈과 함께 사용할 때는 의도한 대로 잘 동작하지 않으므로 주의해야 합니다.

<img src="/assets/img/springcore/core85.png" width="60%" align="center"><br/>

클라이언트 A가 스프링 컨테이너에 프로토타입 빈을 요청했습니다. 스프링 컨테이너는 프로토타입 빈을 새로 생성하여 반환(x01)하고 해당 빈의 count 필드 값은 0입니다. 클라이언트 A는 조회한 프로토타입 빈에 `addCount()`를 호출하면서 count 필드를 +1 했습니다. 결과적으로 프로토타입 빈(x01)의 count는 1이 됩니다.  
프로토타입 스코프의 빈은 호출할 때마다 생성된다고 했습니다. 따라서 클라이언트 B가 동일한 요청을 수행할 때 프로토타입 빈을 x02라고 하면 x02의 count도 0에서 1이 됩니다.

이것을 테스트코드로 확인하면 아래와 같습니다.

```java
package hello.core.scope;

import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Scope;

import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;

import static org.assertj.core.api.Assertions.assertThat;

public class SingletonWithPrototypeTest1 {

  @Test
  void prototypeFind() {
    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(PrototypeBean.class);

    PrototypeBean prototypeBean1 = ac.getBean(PrototypeBean.class);
    prototypeBean1.addCount();
    assertThat(prototypeBean1.getCount()).isEqualTo(1);

    PrototypeBean prototypeBean2 = ac.getBean(PrototypeBean.class);
    prototypeBean2.addCount();
    assertThat(prototypeBean2.getCount()).isEqualTo(1);
  }

  @Scope("prototype")
  static class PrototypeBean {

    private int count = 0;

    public void addCount() {
      count++;
    }

    public int getCount() {
      return count;
    }

    @PostConstruct
    public void init() {
      System.out.println("PrototypeBean.init " + this);
    }

    @PreDestroy
    public void destroy() {
      System.out.println("PrototypeBean.destroy");
    }
  }

}
```

### 싱글톤 빈에서 프로토타입 빈 사용

이번에는 `clientBean`이라는 싱글톤 빈이 의존관계 주입을 통해서 프로토타입 빈을 주입받아서 사용하는 예제를 보겠습니다.

<img src="/assets/img/springcore/core86.png" width="60%" align="center"><br/>

<img src="/assets/img/springcore/core87.png" width="60%" align="center"><br/>

<img src="/assets/img/springcore/core88.png" width="60%" align="center"><br/>

먼저, `clientBean`은 싱글톤이므로 보통 스프링 컨테이너 생성 시점에 함께 생성되고 의존관계 주입도 발생합니다.  
의존관계 자동 주입을 사용하여 주입 시점에 스프링 컨테이너에 프로토타입 빈을 요청하면 스프링 컨테이너는 프로토타입 빈을 생성해서 `clientBean`에 반환합니다. 이 때 프로토타입 빈의 count 필드 값은 0입니다.  
이렇게 반환 받은 프로토타입 빈을 `clientBean`은 내부 필드에 보관합니다. 정확히는 참조값을 보관하는 것입니다.  

클라이언트 A가 `clientBean`을 스프링 컨테이너에 요청해서 받을 때, 싱글톤이므로 항상 같은 `clientBean`이 반환됩니다.  
`clientBean.logic()`을 호출하면 `clientBean`은 prototypeBean의 `addCount()`를 호출해서 프로토타입 빈의 count를 증가시킵니다. 따라서 count는 1이 됩니다.

싱글톤이므로 클라이언트 B가 `clientBean`을 스프링 컨테이너에 요청해서 받으면 당연히 같은 `clientBean`이 반환됩니다. 여기서 중요한 점은 <b>clientBean이 내부에 가지고 있는 프로토타입 빈은 이미 과거에 주입이 끝난 빈입니다. 따라서 주입 시점에 스프링 컨테이너에 요청해서 프로토타입 빈이 새롭게 생성된 것이지 사용할 때마다 새로 생성되는 것이 아닙니다.</b>  
마찬가지로 클라이언트 B가 `clientBean.logic()`을 호출하면 `clientBean`은 prototypeBean의 `addCount()`를 호출해서 프로토타입 빈의 count를 증가시킵니다. 따라서 원래 count 값이 1이었으므로 2가 됩니다.

코드로 확인해보겠습니다.

```java
public class SingletonWithPrototypeTest1 {

  ...

  @Test
  void singletonClientUsePrototype() {
    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(ClientBean.class, PrototypeBean.class);

    ClientBean clientBean1 = ac.getBean(ClientBean.class);
    int count1 = clientBean1.logic();
    assertThat(count1).isEqualTo(1);

    ClientBean clientBean2 = ac.getBean(ClientBean.class);
    int count2 = clientBean2.logic();
    assertThat(count2).isEqualTo(2);
  }

  @Scope("singleton")
  static class ClientBean {
    private final PrototypeBean prototypeBean;

    @Autowired
    public ClientBean(PrototypeBean prototypeBean) {
      this.prototypeBean = prototypeBean;
    }

    public int logic() {
      prototypeBean.addCount();
      return prototypeBean.getCount();
    }
  }

  ...

```

스프링은 일반적으로 싱글톤 빈을 사용하므로 싱글톤 빈이 프로토타입 빈을 사용하게 됩니다. 그런데 싱글톤 빈은 생성 시점에서만 의존관계 주입을 받기 때문에, 프로토타입 빈이 새로 생성은 되지만, 싱글톤 빈과 함께 계속 유지되는 것이 문제입니다.

프로토타입 빈을 사용하는 이유는 사용할 때마다 새로운 프로토타입 빈이 생성되기 때문인데, 이렇게 동작한다면 싱글톤을 사용하지 프로토타입을 사용할 이유가 없습니다.

---

## 프로토타입 - 싱글톤 빈과 함께 사용 시 Provider로 문제 해결

싱글톤 빈과 프로토타입 빈을 함께 사용할 때, 어떻게 하면 사용할 때 마다 항상 새로운 프로토타입 빈을 생성할 수 있을까요?

### 스프링 컨테이너에 요청

가장 간단한 방법은 싱글톤 빈이 프로토타입을 사용할 때마다 스프링 컨테이너에 요청하는 것입니다.

```java
  ...

  @Test
  void singletonClientUsePrototype() {
    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(ClientBean.class, PrototypeBean.class);

    ClientBean clientBean1 = ac.getBean(ClientBean.class);
    int count1 = clientBean1.logic();
    assertThat(count1).isEqualTo(1);

    ClientBean clientBean2 = ac.getBean(ClientBean.class);
    int count2 = clientBean2.logic();
    assertThat(count2).isEqualTo(1);
  }

  @Scope("singleton")
  static class ClientBean {

    @Autowired
    ApplicationContext applicationContext;

    public int logic() {
      PrototypeBean prototypeBean = applicationContext.getBean(PrototypeBean.class);
      prototypeBean.addCount();
      return prototypeBean.getCount();
    }
  }

  ...
```

실행해보면 `ac.getBean()`을 통해서 항상 새로운 프로토타입 빈이 생성되는 것을 확인할 수 있습니다. 이처럼 의존관계를 외부에서 주입(DI) 받는 것이 아니라 직접 필요한 의존관계를 찾는 것을 Dependency Lookup(DL) 의존관계 조회(탐색)이라고 합니다.  

그런데 이렇게 스프링의 ApplicationContext 전체를 주입받게 되면, 스프링 컨테이너에 종속적인 코드가 되고 단위 테스트도 어려워집니다.  
지금 필요한 기능은 지정한 프로토타입 빈을 컨테이너에서 대신 찾아주는, <b>DL</b>정도의 기능만 제공해주는 무언가가 필요합니다. 그리고 스프링에는 이미 준비되어 있습니다.

### ObjectFactory, ObjectProvider

지정한 빈을 컨테이너에서 대신 찾아주는 DL 서비스를 제공하는 것이 바로 `ObjectProvider`입니다. 과거에는 `ObjectFactory`가 있었는데, 편의 기능을 추가하여 `ObjectProvider`가 만들어졌습니다.

```java
  @Test
  void singletonClientUsePrototype() {
    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(ClientBean.class, PrototypeBean.class);

    ClientBean clientBean1 = ac.getBean(ClientBean.class);
    int count1 = clientBean1.logic();
    assertThat(count1).isEqualTo(1);

    ClientBean clientBean2 = ac.getBean(ClientBean.class);
    int count2 = clientBean2.logic();
    assertThat(count2).isEqualTo(1);
  }

  @Scope("singleton")
  static class ClientBean {

    @Autowired
    private ObjectProvider<PrototypeBean> prototypeBeansProvider;

    public int logic() {
      PrototypeBean prototypeBean = prototypeBeansProvider.getObject();
      prototypeBean.addCount();
      return prototypeBean.getCount();
    }
  }
```

<img src="/assets/img/springcore/core89.png" width="60%" align="center"><br/>

실행해보면 `prototypeBeansProvider.getObject()`를 통해서 새로운 프로토타입 빈이 생성된 것을 볼 수 있습니다. `ObjectProvider`의 `getObject()`를 호출하면 내부에서는 스프링 컨테이너를 통해 해당 빈을 찾아서 반환합니다(DL).  
스프링이 제공하는 기능을 사용하지만, 기능이 단순하므로 단위테스트를 만들거나 mock 코드를 만들기 훨씬 쉬워집니다. `ObjectProvider`는 딱 필요한 DL 정도의 기능만 제공합니다.

참고로 `ObjectFactory`는 기능이 단순하여 `getObject()` 하나만 제공하며, 별도의 라이브러리가 필요없습니다. `ObjectProvider`는 `ObjectFactory`를 상속하여 옵션, 스트림 처리 등의 편의 기능이 많이 있습니다. 그 외에 별도의 라이브러리가 필요없다거나 스프링에 의존한다는 것은 같습니다.

여기서 프로토타입에 사용하는 것이 아니라, 스프링 컨테이너를 통해서 DL 과정을 간단하게 도와준다는 것이 핵심 컨셉입니다.

### JSR-330 Provider

마지막 방법은 `javax.inject.Provider`라는 JSR-330 자바 표준을 사용하는 방법입니다. 이 방법을 사용하려면 `javax.inject:javax.inject:1` 라이브러리를 추가해주어야 합니다.

추가해주면 다음과 같은 Provider를 볼 수 있습니다.

```java
package javax.inject;

public interface Provider<T> {
  T get();
}
```

```java
  @Scope("singleton")
  static class ClientBean {

    @Autowired
    private Provider<PrototypeBean> prototypeBeansProvider;

    public int logic() {
      PrototypeBean prototypeBean = prototypeBeansProvider.get();
      prototypeBean.addCount();
      return prototypeBean.getCount();
    }
  }
```

실행해보면 새로운 프로토타입 빈이 생성되는 것을 확인할 수 있습니다. `ObjectProvider`와 같이 딱 필요한 DL 정도의 기능만 제공합니다.

마찬가지로 `get()` 메서드 하나로 기능이 매우 단순하지만, 별도의 라이브러리가 필요합니다. 물론 자바 표준이므로 스프링이 아닌 다른 컨테이너에서도 사용할 수 있습니다.

### 정리

그렇다면 프로토타입 빈을 언제 사용할까요? 매번 사용할 때 마다 의존관계 주입이 완료된 새로운 객체가 필요할 때 사용하면 됩니다. 하지만 실무에서 웹 애플리케이션을 개발해보면, 싱글톤 빈으로 대부분의 문제를 해결할 수 있기 때문에 프로토타입 빈을 직접적으로 사용하는 일은 매우 드뭅니다.  
`ObjectProvider`, `JSR-330 Provider` 등은 프로토타입 뿐만 아니라 DL이 필요한 경우에는 언제든지 사용할 수 있습니다.

참고로 스프링이 제공하는 메서드에 `@Lookup` 애노테이션도 있지만 위의 방법만으로도 충분합니다.  
코드를 스프링이 아닌 다른 컨테이너에서도 사용할 수 있어야 하는 경우를 제외하면 `ObjectProvider`만으로도 충분합니다.