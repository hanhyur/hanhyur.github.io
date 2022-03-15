---
layout: post
date: 2022-03-15 21:00:00
title: "빈 생명주기 콜백"
description: "스프링 핵심 원리 - 기본편"
subject: Spring
category: [spring]
tags: [spring, basic, oop]
use_math: true
comments: true
---

[스프링 핵심 원리 - 기본편](https://inf.run/bMZx)을 공부하고 정리하는 포스트입니다.

---

# 빈 생명주기 콜백

데이터베이스 커넥션 풀이나, 네트워크 소켓처럼 애플리케이션 시작 시점에 필요한 연결을 미리 해두고, 애플리케이션 종료 시점에 연결을 모두 종료하는 작업을 진행하려면, 객체의 초기화와 종료작업이 필요합니다. 이러한 초기화 작업과 종료 작업을 어떻게 진행하는지 스프링을 통해서 확인해보겠습니다.

간단한게 외부 네트워크에 미리 연결하는 객체를 하나 생성한다고 하겠습니다. 실제로 네트워크에 연결하는 것이 아니라 단순히 문자만 출력하도록 하겠습니다. 이 `NetworkClient`는 애플리케이션 시작 지점에 `connect()`를 호출해서 연결을 맺어두어야 하고, 애플리케이션이 종료되면 `disConnect()`를 호출해서 연결을 끊어야 합니다.

```java
package hello.core.lifecycle;

public class NetworkClient {

  private String url;

  public NetworkClient() {
    System.out.println("생성자 호출, url = " + url);
    connect();
    call("초기화 연결 메세지");
  }

  public void setUrl(String url) {
    this.url = url;
  }

  // 서비스 시작 시 호출
  public void connect() {
    System.out.println("connect : " + url);
  }

  public void call(String message) {
    System.out.println("call : " + url + " message = " + message);
  }

  // 서비스 종료 시 호출
  public void disconnect() {
    System.out.println("close : " + url);
  }
}
```

이제 테스트코드를 작성해보겠습니다. 테스트 코드 내부에 환경설정도 함께 해주겠습니다.

```java
package hello.core.lifecycle;

import org.junit.jupiter.api.Test;
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

public class BeanLifeCycleTest {

  @Test
  public void lifeCycleTest() {
    ConfigurableApplicationContext ac = new AnnotationConfigApplicationContext(LifeCycleConfig.class);
    NetworkClient client = ac.getBean(NetworkClient.class);
    ac.close();
  }

  @Configuration
  static class LifeCycleConfig {

    @Bean
    public NetworkClient networkClient() {
      NetworkClient networkClient = new NetworkClient();
      networkClient.setUrl("http://hello-spring.dev");
      return networkClient;
    }

  }
}
```

실행을 해보면 생각했던 것과 다르게 null 값이 나오는 것을 볼 수 있습니다.

<img src="/assets/img/springcore/core76.png" width="60%" align="center"><br/>

왜냐하면 생성만하고 실제 로직을 호출하지 않았기 때문입니다.  
스프링 빈으로 등록할 때 로직의 생성자 부분에서 url 정보 없이 connect가 호출되는 것을 볼 수 있습니다. 객체를 생성하는 단계에서는 url이 없고, 객체를 생성한 다음 외부에서 수정자 주입을 통해 `setUrl()`이 호출되어야 url이 존재할 수 있습니다.

스프링 빈은 간단하게 '객체 생성 -> 의존관계 생성'와 같은 라이프 사이클을 가집니다. 왜냐면 객체를 다 생성해놓아야 의존관계 주입을 할 수 있기 때문입니다. 물론 생성자 주입과 같은 예외가 있습니다. 생성자는 객체를 만들 때 이미 파라미터에 스프링 빈이 들어와야 하기 때문입니다.

스프링 빈은 객체를 생성하고, 의존관계 주입이 다 끝난 다음에야 필요한 데이터를 사용할 수 있는 준비가 완료됩니다. 예를 들어 Setter로 자동 의존관계를 주입했으면 객체를 생성하는 단계에서는 값이 없습니다. 거기서 초기화하거나 연결하면 당연히 비어있습니다.  
초기화는 객체를 생성하는게 아니라 객체에 값이 다 연결되어 있고 외부와 연결되어서 일을 시작하는 것을 말합니다. 따라서 초기화 작업은 의존관계 주입이 모두 완료되고 난 다음에 호출되어야 합니다.  

그러면 개발자는 의존관계 주입이 모두 완료되는 시점을 어떻게 알 수 있을까요?  
스프링은 <b>의존관계 주입이 완료되면 스프링 빈에게 콜백 메서드를 통해서 초기화 시점을 알려주는 다양한 기능을 제공</b>합니다. 또한 스프링은 스프링 컨테이너가 종료되기 직전에 소멸 콜백을 줍니다. 따라서 안전하게 종료 작업을 진행할 수 있습니다.

스프링 빈의 이벤트 라이프사이클은 다음과 같습니다.

<b>스프링 컨테이너 생성 -> 스프링 빈 생성 -> 의존관계 주입 -> 초기화 콜백 -> 사용 -> 소멸 전 콜백 -> 스프링 종료</b>

- 초기화 콜백 : 빈이 생성되고, 빈의 의존관계 주입이 완료된 후 호출
- 소멸전 콜백 : 빈이 소멸되기 직전에 호출

---

### 참고: 객체의 생성과 초기화를 분리하자.

생성자는 필수 정보(파라미터)를 받고, 메모리를 할당해서 객체를 생성하는 책임을 가집니다. 반면에 초기화는 이렇게 생성된 값들을 활용해서 외부 커넥션을 연결하는 등 무거운 동작을 수행합니다.  
따라서 생성자 안에서 무거운 초기화 작업을 함께 하는 것 보다는 객체를 생성하는 부분과 초기화 하는 부분을 명확하게 나누는 것이 유지보수 관점에서 좋습니다. 물론 초기화 작업이 내부 값들만 약간 변경하는
정도로 단순한 경우에는 생성자에서 한번에 다 처리하는게 더 나을 수 있습니다.

---

스프링은 크게 3가지 방법으로 빈 생명주기 콜백을 지원합니다.

- 인터페이스 InitializingBean, DisposableBean
- 설정 정보에 초기화 메서드, 종료 메서드 지정
- @PostConstruct, @PreDestroy

---

## 인터페이스 InitializingBean, DisposableBean