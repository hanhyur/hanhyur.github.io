---
layout: post
date: 2021-03-29 20:46:00
title: "IoC 컨테이너 6부"
description: "스프링 프레임워크 핵심 기술"
subject: Spring framework
category: [ spring ]
tags: [ spring, IoC, profile, properties ]
use_math: true
comments: true
---

[스프링 프레임워크 핵심기술](https://www.inflearn.com/course/spring-framework_core/dashboard)을 공부하고 정리하는 포스트입니다.

---

# Environment

&nbsp;&nbsp;&nbsp;Environment는 ApplicationContext의 기능 중 하나로 `EnvironmentCapable`이라는 인터페이스를 상속받은 것이다. `EnvironmentCapable`이 제공하는 기능이 두 가지가 있는데 바로 프로파일(Profile)과 프로퍼티(Properties)이다.

# 프로파일 Profile

&nbsp;&nbsp;&nbsp;프로파일은 빈들을 묶은 빈들의 그룹 또는 어떠한 환경이라고 할 수 있다. 그렇다면 왜 빈들을 묶어서 <b>환경</b>을 만들어주는걸까?

테스트 환경에서 어떠한 빈들을 사용하겠다, 실제 프로덕션에서는 이러한 빈들을 쓰겠다고 스테이징을 한다. 이처럼 각각의 환경에 따라 다른 빈을 써야하는 경우나 특정 환경에서만 어떠한 빈을 등록해야하는 경우와 같은 요구사항을 충족시키기 위해 프로파일 기능이 추가된 것이다.

이 기능을 `ApplicationContext`의 `Environment`를 통해서 사용할 수 있다.

예제를 통해서 좀 더 자세히 알아보자.

```java
package me.gracenam.demospring51;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.ApplicationArguments;
import org.springframework.boot.ApplicationRunner;
import org.springframework.context.ApplicationContext;
import org.springframework.core.env.Environment;
import org.springframework.stereotype.Component;

import java.util.Arrays;

@Component
public class AppRunner implements ApplicationRunner {

    @Autowired
    ApplicationContext ctx;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        Environment environment = ctx.getEnvironment();
        System.out.println(Arrays.toString(environment.getActiveProfiles()));
        System.out.println(Arrays.toString(environment.getDefaultProfiles()));
    }
}
```

여기서 사용된 `getEnvironment()`는 `EnvironmentCapable`에서 가져온 것으로 `ApplicationContext`는 `EnvironmentCapable`를 상속받는다.

예제를 실행해보면 `getActiveProfiles`은 비어있고, `getDefaultProfiles`은 <b>default</b>라는 이름의 프로파일이 적용되어 있다.

<img src="/assets/img/study/profile01.png" width="70%" align="center"><br/>

default라는 이름의 프로파일은 특별히 설정하지 않아도 항상 적용되는 기본 프로파일이다.

## 프로파일 정의하는 법

<span style="font-size:16pt"><b>&#9654; 클래스에 정의</b></span>

&nbsp;&nbsp;&nbsp;프로파일을 정의하는 첫번째 방법은 프로파일이 정의되어 있는 Configuration 클래스를 만드는 것이다.

```java
package me.gracenam.demospring51;

public interface BookRepository {
}
```

```java
package me.gracenam.demospring51;

public class TestBookRepository implements BookRepository {
}
```

```java
package me.gracenam.demospring51;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;

@Configuration
@Profile("test")
public class TestConfiguration {

    @Bean
    public BookRepository bookRepository() {
        return new TestBookRepository();
    }

}
```

&nbsp;&nbsp;&nbsp;위의 빈 설정 파일은 <b>'test'</b> 프로파일 일 때만 사용이 되는 빈 설정 파일이다. test라는 프로파일로 애플리케이션을 실행하기 전까지 이 빈 설정 파일이 적용되지 않는다.

```java
package me.gracenam.demospring51;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.ApplicationArguments;
import org.springframework.boot.ApplicationRunner;
import org.springframework.context.ApplicationContext;
import org.springframework.core.env.Environment;
import org.springframework.stereotype.Component;

import java.util.Arrays;

@Component
public class AppRunner implements ApplicationRunner {

    @Autowired
    ApplicationContext ctx;

    @Autowired
    BookRepository bookRepository;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        Environment environment = ctx.getEnvironment();
        System.out.println(Arrays.toString(environment.getActiveProfiles()));
        System.out.println(Arrays.toString(environment.getDefaultProfiles()));
    }
}
```

애플리케이션에서 `BookRepository`를 주입받아 실행해보아도 당연히 에러가 발생한다. test 프로파일로 실행하지 않았기 때문에 `BookRepository`가 빈으로 등록되지 않은 것이다.

<img src="/assets/img/study/profile02.png" width="70%" aling="center"><br/>

그렇다면 이것을 해결하기 위해서는 프로파일을 지정해주면 된다. 프로파일을 지정하는 방법은 IntelliJ로 설명하겠다.

<img src="/assets/img/study/profile03.png" width="70%" align="center"><br/>

기본적인 방법은 VM options(1번)에 `-Dspring.profiles.avtive="test"`라고 입력하면 된다. 또는 간단하게 Active profiles(2번) 옵션에 test라고 적어주면 된다.

test 프로파일을 지정해준 뒤 다시 실행해보면 아래와 같이 정상적으로 동작하는 것을 볼 수 있다.

<img src="/assets/img/study/profile04.png" width="70%" align="center"><br/>

<span style="font-size:16pt"><b>&#9654; 빈에 정의</b></span>

&nbsp;&nbsp;&nbsp;프로파일을 정의하는 또 다른 방법은 빈에 정의하는 것이다.

```java
package me.gracenam.demospring51;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;

@Configuration
public class TestConfiguration {

    @Bean
    @Profile("test")
    public BookRepository bookRepository() {
        return new TestBookRepository();
    }

}
```

하지만 이 경우 일일히 다 설정을 해줘야해서 매우 불편하니 권장하지 않는다.

<span style="font-size:16pt"><b>&#9654; ComponentScan 대상에 정의</b></span>

&nbsp;&nbsp;&nbsp;ComponentScan으로 등록되는 빈에도 프로파일을 지정할 수 있다.

```java
package me.gracenam.demospring51;

import org.springframework.context.annotation.Profile;
import org.springframework.stereotype.Repository;

@Repository
@Profile("test")
public class TestBookRepository implements BookRepository {
}
```

## 프로파일 표현식

&nbsp;&nbsp;&nbsp;프로파일을 지정할 때 표현식을 사용할 수 있다. 사용할 수 있는 표현식은 3가지 인데

+ !(not)
+ &(and)
+ \|(or)

로 다음과 같이 사용할 수 있다. 이런 식으로 사용이 가능하다는 정도만 알아두자.

```java
package me.gracenam.demospring51;

import org.springframework.context.annotation.Profile;
import org.springframework.stereotype.Repository;

@Repository
@Profile("!prod & test")
public class TestBookRepository implements BookRepository {
}
```

# 프로퍼티 Properties

&nbsp;&nbsp;&nbsp;프로퍼티는 다양한 방법으로 정의할 수 있는 설정값을 말한다. Environment에서 제공하는 기능은 애플리케이션에 등록된 프로퍼티에 접근할 수 있는 기능으로 프로퍼티 소스 설정과 프로퍼티 값을 가져올 수 있다.

프로퍼티에 접근을 할 때 계층형으로 접근을 하는데 여기서 계층형의 의미는 프로퍼티에 우선순위가 존재하다는 것이다. 프로퍼티의 우선순위는 다음과 같다.

&#9654; 프로퍼티 우선순위

+ ServletConfig 매개변수
+ ServletContext 매개변수
+ JNDI (java:comp/env/)
+ JVM 시스템 프로퍼티 (-Dkey="value")
+ JVM 시스템 환경 변수 (운영 체제 환경 변수)

예제를 통해서 좀 더 자세히 알아보자.

&#9654; VM options를 사용하는 방법(JVM 시스템 프로퍼티로 넘기는 법)

&nbsp;&nbsp;&nbsp;먼저, VM options에 값으로 `-Dapp.name=spring5`를 입력한다. 그리고 난 후 app.name을 출력해보면 spring5가 출력된다.

<img src="/assets/img/study/property01.png" width="70%" align="center"><br/>

```java
package me.gracenam.demospring51;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.ApplicationArguments;
import org.springframework.boot.ApplicationRunner;
import org.springframework.context.ApplicationContext;
import org.springframework.core.env.Environment;
import org.springframework.stereotype.Component;

import java.util.Arrays;

@Component
public class AppRunner implements ApplicationRunner {

    @Autowired
    ApplicationContext ctx;

    @Autowired
    BookRepository bookRepository;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        Environment environment = ctx.getEnvironment();
        System.out.println(environment.getProperty("app.name"));
    }
}
```

<img src="/assets/img/study/property02.png" width="70%" align="center"><br/>

&#9654; properties 파일을 생성하는 방법(프로퍼티 소스로 넣는 법)

&nbsp;&nbsp;&nbsp;좀 더 체계적으로 값을 전달하고 싶은 경우 properties 파일을 사용하면 된다. resource 폴더 아래에 <b>app.properties</b> 파일을 하나 만든 후 아래와 같은 내용을 입력해 준다.

```
app.about=spring
```

그리고 이 프로퍼티 파일을 사용하기 위해 Configuration이 들어있는 클래스에서 `@PropertySource`라는 애노테이션을 사용해서 클래스 패스에 프로퍼티 파일을 놓겠다고 선언해 준다. 이제 프로퍼티 소스를 통해서 Environment에 들어가면 꺼내서 쓸 수 있다.

```java
package me.gracenam.demospring51;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.PropertySource;

@SpringBootApplication
@PropertySource("classpath:/app.properties")
public class Demospring51Application {

    public static void main(String[] args) {
        SpringApplication.run(Demospring51Application.class, args);
    }

}
```

```java
package me.gracenam.demospring51;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.ApplicationArguments;
import org.springframework.boot.ApplicationRunner;
import org.springframework.context.ApplicationContext;
import org.springframework.core.env.Environment;
import org.springframework.stereotype.Component;

import java.util.Arrays;

@Component
public class AppRunner implements ApplicationRunner {

    @Autowired
    ApplicationContext ctx;

    @Autowired
    BookRepository bookRepository;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        Environment environment = ctx.getEnvironment();
        System.out.println(environment.getProperty("app.name"));
        System.out.println(environment.getProperty("app.about"));
    }
}
```

<img src="/assets/img/study/property03.png" width="70%" align="center"><br/>

그렇다면 둘 중에 어떤 것이 우선순위가 더 높을까? 프로퍼티 소스로 넣은게 높을까 JVM 시스템 프로퍼티에 넘겨준게 높을까?

VM options와 프로퍼티 파일 모두 `app.name`을 사용한 후 출력을 해보면 다음과 같은 결과를 얻을 수 있다.

<img src="/assets/img/study/property04.png" width="70%" align="center"><br/>

이유는 프로퍼티가 hierarchical(계층적)이기 때문이다.

---
**Reference**
+ [스프링 프레임워크 핵심기술](https://www.inflearn.com/course/spring-framework_core/dashboard)
