---
layout: post
date: 2022-03-22 21:33:00
title: "빈 스코프 2부"
description: "스프링 핵심 원리 - 기본편"
subject: Spring
category: [spring]
tags: [spring, basic, oop]
use_math: true
comments: true
---

[스프링 핵심 원리 - 기본편](https://inf.run/bMZx)을 공부하고 정리하는 포스트입니다.

---

## 웹 스코프

싱글톤과 프로토타입 스코프를 학습했습니다. 싱글톤은 스프링 컨테이너의 시작부터 끝까지 함께하는 매우 긴 스코프이고, 프로토타입은 생성과 의존관계 주입, 그리고 초기화까지만 진행하는 특별한 스코프입니다.

이제 이어서 웹 스코프에 대해서 알아보겠습니다.

### 웹 스코프 특징

웹 스코프는 웹 환경에서만 동작합니다. 프로토타입과 다르게 스프링이 해당 스코프의 종료시점까지 관리합니다. 즉, 종료 메서드가 호출됩니다.

### 웹 스코프 종류

웹 스코프에는 4가지가 있습니다.

`request`는 HTTP 요청 하나가 들어오고 나갈 때까지 유지되는 스코프입니다. 각각의 HTTP 요청마다 별도의 빈 인스턴스가 생성되고 관리됩니다.

<img src="/assets/img/springcore/core90.png" width="60%" align="center"><br/>

클라이언트 A와 B가 동시에 요청을 하면 Controller에서는 각각 다른 스프링 빈이 생성되서 사용이 됩니다. Service에서 request scope의 빈을 요청하면 각각 다른 빈을 받게 됩니다.

`session`은 HTTP Session과 동일한 생명주기를 가지는 스코프입니다.

`application`은 서블릿 컨텍스트(ServletContext)와 동일한 생명주기를 가지는 스코프입니다.

`websocket`은 웹 소켓과 동일한 생명주기를 가지는 스코프입니다.

---

## request 스코프 예제 만들기

### 웹 환경 추가

웹 스코프는 웹 환경에서만 동작하기 때문에 web 환경이 동작하도록 라이브러리를 추가해주겠습니다.

```java
//web 라이브러리 추가
implementation 'org.springframework.boot:spring-boot-starter-web'
```

이제 `hello.core.CoreApplication`의 main 메서드를 실행하면 웹 애플리케이션이 실행되는 것을 볼 수 있습니다.

<img src="/assets/img/springcore/core91.png" width="60%" align="center"><br/>

참고로 `spring-boot-starter-web` 라이브러리를 추가하면 스프링 부트는 내장 톰캣 서버를 활용해서 웹 서버와 스프링을 함께 실행시킵니다.  

스프링 부트는 웹 라이브러리가 없으면 지금까지 사용해온 `AnnotationConfigApplicationContext`를 기반으로 애플리케이션을 구동합니다. 웹 라이브러리가 추가되면 웹과 관련된 추가 설정과 환경들이 필요하므로 `AnnotationConfigServletWebServerApplicationContext`를 기반으로 애플리케이션을 구동합니다.

### request 스코프 예제 개발

동시에 여러 HTTP 요청이 오면 정확히 어떤 요청이 남긴 로그인지 구분하기 어렵습니다. 이럴 때 사용하기 좋은 것이 바로 <b>request 스코프</b>입니다.

```java
package hello.core.common;

import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;

import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;
import java.util.UUID;

@Component
@Scope(value = "request")
public class MyLogger {

  private String uuid;
  private String requestURL;

  public void setRequestURL(String requestURL) {
    this.requestURL = requestURL;
  }

  public void log(String message) {
    System.out.println("[ " + uuid + " ] " + " [ " + requestURL + " ] " + message);
  }

  @PostConstruct
  public void init() {
    uuid = UUID.randomUUID().toString();
    System.out.println(" [ " + uuid + " ] request scope bean create : " + this);
  }

  @PreDestroy
  public void close() {
    System.out.println(" [ " + uuid + " ] request scope bean close : " + this);
  }
  
}
```

먼저 Log를 출력하기 위해 `MyLogger` 클래스를 만들었습니다. `@scope(value = "request")`를 사용해서 request 스코프로 지정했습니다. 이제 이 빈은 HTTP 요청 당 하나씩 생성되고, HTTP 요청이 끝나는 시점에 소멸됩니다.  
이 빈이 생성되는 시점에 자동으로 `@PostConstruct` 초기화 메서드를 사용해서 uuid를 생성해서 저장해둡니다. HTTP 요청 당 하나씩 생성되므로, uuid를 저장해두면 다른 HTTP 요청과 구분할 수 있습니다. 그리고 소멸하는 시점에 `@PreDestroy`를 사용해서 종료 메시지를 남깁니다.  
`requestURL`은 이 빈이 생성되는 시점에는 알 수 없기 때문에, 외부에서 setter로 입력받도록 했습니다.

```java
package hello.core.web;

import hello.core.common.MyLogger;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import javax.servlet.http.HttpServletRequest;

@Controller
@RequiredArgsConstructor
public class LogDemoController {

  private final LogDemoService logDemoService;
  private final MyLogger myLogger;

  @RequestMapping("log-demo")
  @ResponseBody
  public String logDemo(HttpServletRequest request) {
    String requestURL = request.getRequestURL().toString();
    myLogger.setRequestURL(requestURL);

    myLogger.log("controller test");
    logDemoService.logic("testId");
    return "OK";
  }

}
```

로거가 잘 작동하는지 확인하는 테스트용 컨트롤러입니다.

`HttpServletRequest`를 통해서 요청 URL을 받습니다. 이렇게 받은 requestURL 값을 myLogger에 저장합니다. myLogger는 HTTP 요청 당 각각 구분되므로 다른 HTTP 요청 때문에 값이 섞이는 경우는 없습니다.

컨트롤러에서는 "controller test"라는 로그를 남깁니다.

참고로 requestURL을 MyLogger에 저장하는 부분은 컨트롤러 보다는 공통 처리가 가능한 스프링 인터셉터나 서블릿 필터 같은 곳을 활용하는 것이 좋습니다.

```java
package hello.core.logdemo;

import hello.core.common.MyLogger;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;

@Service
@RequiredArgsConstructor
public class LogDemoService {

  private final MyLogger myLogger;

  public void logic(String id) {
    myLogger.log("service id = " + id);
  }
  
}
```

비즈니스 로직이 있는 서비스 계층에서도 로그를 출력해보겠습니다. 

여기서 중요한 점은 request Scope를 사용하지 않고 파라미터로 모든 정보를 서비스 계층에 넘긴다면, 파라미터가 많아서 지저분해집니다. 더 큰 문제는 requestURL 같은 웹과 관련된 정보가 웹과 관련없는 서비스 계층까지 넘어가게 된다는 것입니다. 웹과 관련된 부분은 컨트롤러까지만 사용해야 합니다. 서비스 계층은 웹 기술에 종속되지 않고, 가급적 순수하게 유지하는 것이 유지보수 관점에서 좋습니다.

request Scope의 MyLogger 덕분에 이런 부분을 파라미터로 넘기지 않고, MyLogger의 멤버변수에 저장해 코드와 계층을 깔끔하게 유지할 수 있습니다.

이제 실행을 해보겠습니다.

<img src="/assets/img/springcore/core92.png" width="70%" align="center"><br/>

스프링 애플리케이션을 실행시키면 기대한 것과 달리 오류가 발생합니다. 메시지 마지막에 싱글톤이라는 단어가 나오고, 현재 스레드에서 request Scope이 active 되지 않았다고 되어 있습니다. 이게 무슨 말일까요?

스프링 애플리케이션이 실행될 때 스프링 컨테이너는 컨트롤러를 스프링 빈에 등록해야 합니다. 이 때 의존관계 주입이 일어나는데, 스프링 컨테이너에게 MyLogger를 요구합니다. MyLogger는 request Scope이기 때문에 요청이 들어와야 생성됩니다. 그런데 지금 이 단계에서는 요청이 없습니다.  
즉, 스프링 애플리케이션을 실행하는 시점에 싱글톤 빈은 생성해서 주입이 가능하지만, request 스코프 빈은 아직 생성되지 않았습니다! 

그러면 이것을 어떻게 해결할 수 있을까요? 바로 Provider를 사용하면 됩니다.

---

## 스코프와 Provider

ObjectProvider를 사용해서 문제를 해결해보겠습니다.

```java
@Controller
@RequiredArgsConstructor
public class LogDemoController {

  private final LogDemoService logDemoService;
  private final ObjectProvider<MyLogger> myLoggerProvider;

  @RequestMapping("log-demo")
  @ResponseBody
  public String logDemo(HttpServletRequest request) {
    String requestURL = request.getRequestURL().toString();

    MyLogger myLogger = myLoggerProvider.getObject();
    myLogger.setRequestURL(requestURL);

    myLogger.log("controller test");
    logDemoService.logic("testId");
    return "OK";
  }

}
```

```java
@Service
@RequiredArgsConstructor
public class LogDemoService {

  private final ObjectProvider<MyLogger> myLoggerProvider;

  public void logic(String id) {
    MyLogger myLogger = myLoggerProvider.getObject();
    myLogger.log("service id = " + id);
  }

}
```

`ObjectProvider`를 사용해서 `ObjectProvider.getObject()`를 호출하는 시점까지 request scope 빈의 생성을 지연시켰습니다. 정확한 표현은 스프링 컨테이너에 요청하는 것을 지연한 것입니다. `ObjectProvider.getObject()`가 호출되는 시점은 HTTP의 요청이 진행중이기 때문에 request scope 빈의 생성이 정상적으로 처리됩니다.

`LogDemoController`와 `LogDemoService`가 각각 한번씩 호출해도 같은 HTTP 요청이면 같은 스프링 빈이 반환됩니다. 이것은 직접 구현하려면 엄청 어렵습니다. 하지만 스프링을 사용하면 아주 간편하게 할 수 있습니다.

---

## 스코프와 프록시

이번에는 프록시 방식을 알아보겠습니다.

```java
@Component
@Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)
public class MyLogger {

  ...

```

`proxyMode = ScopedProxyMode.TARGET_CLASS`가 핵심입니다. 이 프록시 모드는 적용 대상이 인터페이스가 아닌 클래스이면 `TARGET_CLASS`를 선택하고 인터페이스이면 `INTERFACE`를 선택하면 됩니다.

이렇게 추가해주면 MyLogger의 가짜 프록시 클래스를 만들어두고 HTTP request와 상관 없이 가짜 프록시 클래스를 다른 빈에 미리 주입해 둘 수 있습니다.

기존 코드에서 Provider를 모두 제거하고 실행해보면 잘 실행되는 것을 확인할 수 있습니다. Provider가 없는데 어떻게 동작을 하는 것일까요?

### 동작 원리

`myLogger`를 출력해서 한번 확인해보겠습니다.

<img src="/assets/img/springcore/core93.png" width="70%" align="center"><br/>

어디선가 많이 본 듯한 스프링이 조작한 것, `CGLIB`가 출력된 것을 볼 수 있습니다.

<b>CGLIB</b> 라이브러리로 내 클래스를 상속 받은 가짜 프록시 객체를 만들어서 주입하는 것이었습니다.  

`@Scope`의 `proxyMode = ScopedProxyMode.TARGET_CLASS`를 설정하면 스프링 컨테이너는 CGLIB라는 바이트코드를 조작하는 라이브러리를 사용해서, MyLogger를 상속받은 가짜 프록시 객체를 생성합니다. 결과를 확인해보면 우리가 등록한 순수한 MyLogger 클래스가 아니라 `MyLogger$$EnhancerBySpringCGLIB`이라는 클래스로 만들어진 객체가 대신 등록된 것을 확인할 수 있습니다.  
그리고 스프링 컨테이너에 "myLogger"라는 이름으로 진짜 대신 이 가짜 프록시 객체를 등록합니다. `ac.getBean("myLogger", MyLogger.class)`로 조회해도 프록시 객체가 조회됩니다. 그래서 의존관계 주입도 이 가짜 프록시 객체가 주입됩니다.

<img src="/assets/img/springcore/core94.png" width="70%" align="center"><br/>

가짜 프록시 객체는 요청이 오면 그때 내부에서 진짜 빈을 요청하는 위임 로직이 들어있습니다. 클라이언트가 `myLogger.logic()`을 호출하면 사실은 가짜 프록시 객체의 메서드를 호출한 것입니다. 가짜 프록시 객체는 request 스코프의 진짜 `myLogger.logic()`를 호출합니다. 

가짜 프록시 객체는 원본 클래스를 상속 받아서 만들어졌기 때문에 이 객체를 사용하는 클라이언트 입장에서는 사실 원본인지 아닌지도 모르게, 동일하게 사용할 수 있습니다.(다형성)

### 정리

- CGLIB라는 라이브러리로 내 클래스를 상속받은 가짜 프록시 객체를 만들어서 주입합니다.
- 가짜 프록시 객체는 실제 요청이 오면 그 때 내부에서 실제 빈을 요청하는 위임 로직이 들어있습니다.
- 가짜 프록시 객체는 실제 request scope와는 관계가 없습니다. 그저 가짜이고, 내부에 단순한 위임 로직만 있고 싱글톤처럼 동작합니다.

### 특징

프록시 객체 덕분에 클라이언트는 마치 싱글톤 빈을 사용하듯이 편리하게 request scope를 사용할 수 있습니다. 사실 Provider를 사용하든, 프록시를 사용하든 핵심 아이디어는 진짜 객체 조회를 꼭 필요한 시점까지 지연처리 한다는 점입니다.  
단지 애노테이션 설정 변경만으로 원본 객체를 프록시 객체로 대체할 수 있습니다. 이것이 바로 다형성과 DI 컨테이너가 가진 큰 강점입니다.  
참고로 꼭 웹 스코프가 아니어도 프록시는 사용할 수 있습니다.

### 주의

마치 싱글톤을 사용하는 것 같지만 다르게 동작하기 때문에 주의해서 사용해야 합니다. 이런 특별한 scope는 꼭 필요한 곳에서만 최소한으로 사용해야 합니다.