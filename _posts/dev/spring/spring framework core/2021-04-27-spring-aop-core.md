---
layout: post
date: 2021-04-27 10:00:00
title: "스프링 AOP 총정리 : 개념, 프록시 기반 AOP, @AOP"
description: "스프링 프레임워크 핵심 기술"
subject: Spring framework
category: [ spring ]
tags: [ spring, IoC, aop, proxy ]
use_math: true
comments: true
---

[스프링 프레임워크 핵심기술](https://www.inflearn.com/course/spring-framework_core/dashboard)을 공부하고 정리하는 포스트입니다.

---

# 스프링 AOP

&nbsp;&nbsp;&nbsp;<b>AOP</b>란 <b>Aspect-oriented Programming</b>의 약어로 스프링 AOP는 AOP의 구현체를 제공하며, 자바에 만들어져 있는 프레임워크 <b>AspectJ</b>[^1]라는 또 다른 구현체와 연동해서 사용할 수 있는 기능을 제공한다. 이러한 기능을 기반으로 스프링 트랜잭션과 같은 다른 기능이 적용되고 있다.

그렇다면 AOP는 정확히 무엇일까? AOP, 관점 지향 프로그래밍은 흩어진 Aspect를 모듈화 할 수 있는 프로그래밍 기법을 말한다. 관점 지향은 어떤 로직을 기준으로 핵심적인 관점, 부가적인 관점으로 나누어 본다는 말이고 따라서 관점을 기준으로 각각 모듈화하는 프로그래밍 기법인 것이다. <b>OOP(Object-oriented Programming, 객체 지향 프로그래밍)</b>과 나란히 하는, 서로 보완관계에 있는 기술이다.

&nbsp;&nbsp;&nbsp;AOP에 대한 내용을 그림을 보면서 알아보자.

<img src="/assets/img/study/aop01.png" width="70%" aling="center"><br/>

각각의 색들은 <b>Concern</b>이라고 보면된다. 여기서 Concern이란 여러 클래스, 메서드에 걸쳐서 나타나는 비슷한 코드들을 의미한다.

각 클래스에 있는 <b>Crosscutting Concerns</b>, 흩어진 관심사를 <b>Aspect로 모듈화하고 핵심적인 비즈니스 로직에서 분리하여 재사용하겠다는 것</b>이 AOP의 취지이다.

## AOP 주요 개념

<span style="font-size:16pt"><b>&#9654; Aspect</b></span>

&nbsp;&nbsp;&nbsp;흩어진 관심사(Crosscutting Concerns)를 묶어서 모듈화 한 것. 하나의 모듈. <b>Advice</b>와 <b>Point Cut</b>이 들어간다.

<span style="font-size:16pt"><b>&#9654; Target</b></span>

&nbsp;&nbsp;&nbsp;Aspect가 가지고 있는 Advice가 적용되는 대상(클래스, 메서드 등등)을 말한다.

<span style="font-size:16pt"><b>&#9654; Advice</b></span>

&nbsp;&nbsp;&nbsp;어떤 일을 해야할 지에 대한 것. 해야할 일들에 대한 정보를 가지고 있다.

<span style="font-size:16pt"><b>&#9654; Join Point</b></span>

&nbsp;&nbsp;&nbsp;가장 흔한 Join Point는 <b>메서드 실행 시점</b>이다. Advice가 적용될 위치, 끼어들 수 있는 지점. 생성자 호출 직전, 생성자 호출 시, 필드에 접근하기 전, 필드에서 값을 가져갔을 때 등등.

<span style="font-size:16pt"><b>&#9654; Point Cut</b></span>

&nbsp;&nbsp;&nbsp;Join Point의 상세한 스펙을 정의한 것. 어디에 적용해야 하는지에 대한 정보를 가지고 있다. "A 클래스에 B 메서드를 적용할 때 호출을 해라."와 같은 구체적인 정보를 준다.

## AOP 구현체

<span style="font-size:16pt"><b>&#9654; 자바</b></span>

+ AspectJ
+ 스프링 AOP

<span style="font-size:16pt"><b>&#9654; 그 외</b></span>

+ [위키피디아](https://en.wikipedia.org/wiki/Aspect-oriented_programming#Implementations) 참고

## AOP 적용 방법

<span style="font-size:16pt"><b>&#9654; 컴파일 타임에 적용</b></span>

&nbsp;&nbsp;&nbsp;컴파일 시점(.java 파일을 .class 파일로 만들 때)에 바이트 코드를 조작하여 조작된(AOP가 적용된) 바이트코드를 생성.

<span style="font-size:16pt"><b>&#9654; 로드 타임에 적용</b></span>

&nbsp;&nbsp;&nbsp;순수하게 컴파일한 뒤, 클래스를 로딩하는 시점에 클래스 정보를 변경(Load Time Weaving, 로드타임 위빙).

<span style="font-size:16pt"><b>&#9654; 런타임에 적용</b></span>

&nbsp;&nbsp;&nbsp;스프링 AOP가 사용하는 방법. A 클래스 타입의 Bean을 만들 때 A 타입의 Proxy Bean[^2]을 만들어 Proxy Bean이 Aspect 코드를 추가하여 동작.

# 프록시 기반 AOP

## 스프링 AOP 특징

&nbsp;&nbsp;&nbsp;스프링 AOP의 특징에 대해서 알아보자.

+ 프록시 기반의 AOP 구현체이다. 프록시 객체를 사용하는 이유는 접근 제어 및 부가 기능을 추가하기 위해서이다.
+ 스프링 빈에만 AOP를 적용할 수 있다.
+ 모든 AOP 기능을 제공하는 것이 목적이 아니라, 스프링 IoC와 연동하여 엔터프라이즈 애플리케이션에서 가장 흔한 문제(중복코드, 프록시 클래스 작성의 번거로움, 객체 간 관계 복잡도 증가 등등...)를 해결하기 위한 솔루션을 제공하는 것이 목적.

## 프록시 패턴

<img src="/assets/img/study/aop02.png" width="70%" align="center"><br/>

&nbsp;&nbsp;&nbsp;프록시 패턴에는 <b>interface</b>가 존재하고 <b>Client</b>는 이 interface 타입으로 Proxy 객체를 사용하게 된다.

Proxy 객체는 기존의 타겟 객체(Real Subject)를 참조하고 있다. Proxy 객체와 Real Subject의 타입은 같고, Proxy는 원래 해야 할 일을 가지고 있는 Real Subject를 감싸서 Client의 요청을 처리한다.

### 왜? 이렇게 해서 패턴을 사용하는 걸까?
그 이유는 <b>기존 코드의 변경 없이 접근 제어 또는 부가 기능 추가</b>를 위해서다.

&#9654; AppRunner.java

```java
package me.gracenam.demospring51;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.ApplicationArguments;
import org.springframework.boot.ApplicationRunner;
import org.springframework.stereotype.Component;

@Component
public class AppRunner implements ApplicationRunner {

    @Autowired
    EventService eventService;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        eventService.createEvent();
        eventService.publishEvent();
    }

}
```

&#9654; EventService.java

```java
package me.gracenam.demospring51;

public interface EventService {

    void createEvent();

    void publishEvent();

}
```

&#9654; SimpleEventService.java

```java
package me.gracenam.demospring51;

import org.springframework.stereotype.Service;

@Service
public class SimpleEventService implements EventService {

    @Override
    public void createEvent() {
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Created an event");
    }

    @Override
    public void publishEvent() {
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Published an event");
    }

}
```

&nbsp;&nbsp;&nbsp;AppRunner는 Client, EventService는 Subject, SimpleEventService는 Real Subject라고 할 수 있다.

EventService 인터페이스를 상속받은 SimpleEventService에 실질적인 동작 코드가 작성되어 있고 AppRunner를 통해서 실행이 된다.

&nbsp;&nbsp;&nbsp;이제 Proxy로 <b>SimpleEventService(Real Subject)와 AppRunner(Client)를 건들이지 않고 성능을 테스트할 수 있는 기능</b>을 추가해보자.

```java
package me.gracenam.demospring51;

import org.springframework.stereotype.Service;

@Service
public class SimpleEventService implements EventService {

    @Override
    public void createEvent() {
        long begin = System.currentTimeMillis();

        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Created an event");

        System.out.println(System.currentTimeMillis() - begin);
    }

    @Override
    public void publishEvent() {
        long begin = System.currentTimeMillis();

        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Published an event");

        System.out.println(System.currentTimeMillis() - begin);
    }

    public void deleteEvent() {
        System.out.println("Delete an event");
    }

}
```

```java
package me.gracenam.demospring51;

public interface EventService {

    void createEvent();

    void publishEvent();

    void deleteEvent();

}
```

```java
package me.gracenam.demospring51;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.ApplicationArguments;
import org.springframework.boot.ApplicationRunner;
import org.springframework.stereotype.Component;

@Component
public class AppRunner implements ApplicationRunner {

    @Autowired
    EventService eventService;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        eventService.createEvent();
        eventService.publishEvent();
        eventService.deleteEvent();
    }

}
```

<img src="/assets/img/study/aop03.png" width="70%" aling="center"><br/>

&nbsp;&nbsp;&nbsp;먼저, 프로그램의 성능을 테스트 하는 코드를 추가했다. 시작 시간과 종료 시간을 구해서 동작하는데 걸리는 시간을 출력하도록 했는데, 두 개만 측정하고 하나는 측정하지 않도록 했다. 위 코드처럼 작성할 경우에는 기존의 코드를 건드리게 된다.

기존의 코드를 건드리지 않고 하는 방법이 바로 프록시 패턴을 사용하는 것이다.

```java
package me.gracenam.demospring51;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Primary;
import org.springframework.stereotype.Service;

@Primary
@Service
public class ProxySEService implements EventService {

    @Autowired
    SimpleEventService simpleEventService;

    @Override
    public void createEvent() {
        long begin = System.currentTimeMillis();
        simpleEventService.createEvent();
        System.out.println(System.currentTimeMillis() - begin);
    }

    @Override
    public void publishEvent() {
        long begin = System.currentTimeMillis();
        simpleEventService.publishEvent();
        System.out.println(System.currentTimeMillis() - begin);
    }

    @Override
    public void deleteEvent() {
        simpleEventService.deleteEvent();
    }
}
```

`@Primary`는 같은 타입의 빈이 여러가지일 때 그 중 하나를 선택하여 사용하는 애노테이션이다.

`@Autowired`는 이론적으로 인터페이스 타입의 빈을 받는 것이 추천된다. 하지만 여기서 Proxy의 경우 Real Subject의 빈을 주입받아서 사용해야하기 때문에 해당 타입인 SimpleEventService를 명시해주면 주입받아 사용할 수 있다. 또는 EventService 타입을 받지만 빈의 이름(simpleEventService)에 기반해서 주입받아도 상관은 없다.

성능을 테스트하고 싶은 두 개에만 코드를 추가했다. 이렇게 코드를 완성하면 Proxy가 Real Subject를 가지고 있고, Real Subject에 일을 위임해서 대신 처리하고, 부가적인 기능들은 가지고 있다.

Client는 `@Autowired`로 EventService를 주입받지만 `@Primary`로 등록된 빈을 가져다 쓰게 될 것이다.

<img src="/assets/img/study/aop04.png" width="70%" aling="center"><br/>

실행해보면 마찬가지로 실행된 시간이 찍히는 것을 확인할 수 있다.

&nbsp;&nbsp;&nbsp;이렇게 Proxy를 사용해서 만들면 원래의 Client와 Real Subject의 코드를 건드리지 않고 부가기능을 추가할 수 있었다. 하지만 Proxy에 중복코드가 생기고, Proxy 클래스를 만드는데 생기는 비용과 수고가 발생한다는 문제가 있다.

만일 Proxy를 여러 클래스, 여러 메소드에 적용시켜야 한다고 생각해보자. 매번 프록시 클래스를 작성해야하고, 또 작성한 프록시 클래스 내에서 중복이 발생할 것이다.

&nbsp;&nbsp;&nbsp;위 예제에서는 Proxy를 클래스로 만들어서 사용했지만, 동적으로 Proxy 객체를 만드는 방법이 있다. 여기서 동적이란, 런타임, 즉 애플리케이션이 동작하는 중에 동적으로 어떤 객체의 Proxy 객체를 만드는 것을 말한다.

스프링 IoC 컨테이너가 제공하는 기반 시설과 Dynamic 프록시를 사용해서 여러 복잡한 문제(중복 코드, 매번 Proxy를 생성 등등)를 해결할 수 있다. 이것이 바로 <b>Spring AOP</b>이다.

# @AOP

&nbsp;&nbsp;&nbsp;스프링 애노테이션 기반의 AOP를 살펴보자.

먼저, `@AOP`를 사용하기 위해서 의존성을 추가한다.

```
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

이제 Aspect를 나타내는 클래스를 생성한다.

```java
package me.gracenam.demospring51;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;

@Component
@Aspect
public class PerfAspect {

    @Around("execution(* me.gracenam..*.EventService.*(..))")
    public Object logPerf(ProceedingJoinPoint pjp) throws Throwable {
        long begin = System.currentTimeMillis();
        Object retVal = pjp.proceed();
        System.out.println(System.currentTimeMillis() - begin);
        return retVal;
    }

}
```

`@Aspect` 애노테이션으로 이 클래스가 Aspect 클래스임을 알려준다. `@Component` 애노테이션을 사용해서 빈으로 등록하는데 이는 애노테이션 기반의 스프링 IoC를 사용하기 때문에 Component Scan을 통해서 빈 등록을 하기 때문이다.

두 가지 정보가 필요한데 <b>해야할 일</b>과 <b>어디에 적용할 것인가</b>이다. 해야할 일은 <b>Advice</b>, 어디에 적용할 것인가는 <b>Point Cut</b>에 해당하고 이 두 가지를 정의해야 한다.

`ProceedingJoinPoint`, PJP는 Advice가 적용되는 대상이다. 즉, Advice가 적용되는 `createEvent`, `publishEvent`와 같은 메서드 자체라고 보면 된다. method invocation과 비슷한 개념이다.

시간을 측정하고 걸린 시간을 출력해주는 기능을 추가하면 Advice가 완성이 된다. 완성된 Advice는 Around Advice라고 해서 `@Around` 애노테이션을 붙여준다. 그리고 value에 Point Cut 이름을 주거나 직접 정의할 수도 있다.

`execution`은 Point Cut 표현식인데 이 표현식을 사용해서 어디에 적용할 지를 정의할 수 있다. `* me.gracenam..*.EventService.*(..)`는 me.gracenam 패키지 밑에 있는 모든 클래스 중 EventService에 있는 모든 메소드에 Advice를 적용하라고 정의한 것이다. 만일 `EventService`를 `*`로 변경하면 여러 클래스에 적용할 수 있다.

<img src="/assets/img/study/aop05.png" width="70%" aling="center"><br/>

&nbsp;&nbsp;&nbsp;하지만 이렇게 할 경우에는 적용하고 싶지 않은 메서드에도 적용이 될 수 있다. 그럴 땐 execution을 사용하는 대신 애노테이션으로 적용하면 원하는 곳에만 적용할 수 있다.

```java
package me.gracenam.demospring51;

import java.lang.annotation.*;

@Documented
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.CLASS)
public @interface PerfLogging {
}
```

```java
package me.gracenam.demospring51;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;

@Component
@Aspect
public class PerfAspect {

    @Around("@annotation(PerfLogging)")
    public Object logPerf(ProceedingJoinPoint pjp) throws Throwable {
        long begin = System.currentTimeMillis();
        Object retVal = pjp.proceed();
        System.out.println(System.currentTimeMillis() - begin);
        return retVal;
    }

}
```

```java
package me.gracenam.demospring51;

import org.springframework.stereotype.Service;

@Service
public class SimpleEventService implements EventService {

    @PerfLogging
    @Override
    public void createEvent() {
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Created an event");
    }

    @PerfLogging
    @Override
    public void publishEvent() {
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Published an event");
    }

    public void deleteEvent() {
        System.out.println("Delete an event");
    }

}
```

애노테이션을 만들 때 한 가지 주의해야 할 점은 `RetentionPolicy`를 Class 이상으로 주어야 한다. 이 `RetentionPolicy`라는 것은 기본 값이 Class인데 <b>이 애노테이션 정보를 얼마나 유지할 것인가</b>를 말하는 것이다. 즉, 기본 값이 Class라는 것은 '.class 파일까지 유지하겠다'라는 말이다.

Aspect에서는 `execution` 대신 `@annotation`이라는 표현식으로 PerfLogging이라는 애노테이션이 달린 곳에 적용되도록 지정했다.

<img src="/assets/img/study/aop06.png" width="70%" aling="center"><br/>

## 그 외

예제에서는 @Around를 통해서 타겟 메서드의 Aspect 실행 시점을 지정했지만 다른 어노테이션들도 있다.

+ @Before (이전) : 어드바이스 타겟 메소드가 호출되기 전에 어드바이스 기능을 수행
+ @After (이후) : 타겟 메소드의 결과에 관계없이(즉 성공, 예외 관계없이) 타겟 메소드가 완료 되면 어드바이스 기능을 수행
+ @AfterReturning (정상적 반환 이후)타겟 메소드가 성공적으로 결과값을 반환 후에 어드바이스 기능을 수행
+ @AfterThrowing (예외 발생 이후) : 타겟 메소드가 수행 중 예외를 던지게 되면 어드바이스 기능을 수행
+ @Around (메소드 실행 전후) : 어드바이스가 타겟 메소드를 감싸서 타겟 메소드 호출전과 후에 어드바이스 기능을 수행

---
**Reference**
+ [스프링 프레임워크 핵심기술](https://www.inflearn.com/course/spring-framework_core/dashboard)

___
[^1]:PARC에서 개발한 자바용 AOP 확장 기능.
[^2]:Proto Bean을 감싸고 있는 Bean. 일종의 껍질.
