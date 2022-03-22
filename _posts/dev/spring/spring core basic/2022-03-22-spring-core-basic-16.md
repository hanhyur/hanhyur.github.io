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

<img src="/assets/img/springcore/core91.png" width="70%" align="center"><br/>

스프링 애플리케이션을 실행시키면 기대한 것과 달리 오류가 발생합니다. 메시지 마지막에 싱글톤이라는 단어가 나오고, 현재 스레드에서 request Scope이 active 되지 않았다고 되어 있습니다. 이게 무슨 말일까요?

스프링 애플리케이션이 실행될 때 스프링 컨테이너는 컨트롤러를 스프링 빈에 등록해야 합니다. 이 때 의존관계 주입이 일어나는데, 스프링 컨테이너에게 MyLogger를 요구합니다. MyLogger는 request Scope이기 때문에 요청이 들어와야 생성됩니다. 그런데 지금 이 단계에서는 요청이 없습니다.  
즉, 스프링 애플리케이션을 실행하는 시점에 싱글톤 빈은 생성해서 주입이 가능하지만, request 스코프 빈은 아직 생성되지 않았습니다! 

그러면 이것을 어떻게 해결할 수 있을까요? 바로 Provider를 사용하면 됩니다.

---

## 스코프와 Provider

