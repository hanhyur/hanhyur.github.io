---
layout: post
date: 2021-05-15 23:00:00
title: "스프링 부트 활용 : 외부 설정 1부"
description: "스프링 부트 개념과 활용"
subject: Spring Boot
category: [ spring boot ]
tags: [ spring, boot, external config ]
use_math: true
comments: true
---

# 스프링 부트 활용

## 외부 설정

&nbsp;&nbsp;&nbsp;외부 설정은 애플리케이션에서 사용하는 여러 설정 값들을 애플리케이션 밖이나 안에 정의할 수 있는 기능이다.  
흔하게 볼 수 있는 설정 파일은 <b>application.properties</b>라는 파일이다. 이 파일은 스프링 부트가 애플리케이션을 구동할 때 자동으로 로딩하는 파일로 일종의 컨벤션이다.

이처럼 정의되어 있는 파일 안에 key-value 형태로 값을 정의하면 애플리케이션에서 참조해서 사용할 수 있다. 참조해서 사용하는 방법은 여러가지인데 그 중에서 가장 기본적인 방법은 아래와 같이 @Value 애노테이션을 사용하는 것이다.

```
grace.name = grace
```

```java
package me.gracenam.springinit;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.ApplicationArguments;
import org.springframework.boot.ApplicationRunner;
import org.springframework.stereotype.Component;

@Component
public class SampleRunner implements ApplicationRunner {

    @Value("${grace.name}")
    public String name;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println("=================");
        System.out.println(name);
        System.out.println("=================");
    }

}
```

이렇게 한 후 실행해보면 간단하게 정의한 값이 출력되는 것을 확인할 수 있다.

<img src="/assets/img/study/ex01.png" width="50%" height="auto" align="center"><br/>

&#9654; 랜덤 값과 place holder

properties를 작성할 때 임의의 값을 넣고싶을 수도 있다. 그럴 때 사용할 수 있는 것이 랜덤 값이다.

```
key = ${random.*}
```

`random.*`에서 *로 올 수 있는 값들은 다음과 같다. 단, int 값의 value와 MAX 값을 사용할 때는 공백이 들어가서는 안된다.

<img src="/assets/img/study/ex06.png" width="70%" heigth="auto" align="center"><br/>

properties에 한 번 정의된 값은 재사용할 수 있다. 이런 것을 place holder라고 한다.

```
name = grace
fullName = ${name} nam
```

## 프로퍼티 우선순위

&nbsp;&nbsp;&nbsp;프로퍼티를 정의하는 방법은 굉장히 다양하기 떄문에 이에 따른 혼란이 있을 수 있다. 이를 방지하기 위해서 우선순위가 있는데 위에 사용한 방법은 15위이다.

&#9654; 우선 순위

1. 홈 디렉토리에 있는 <b>spring-boot-dev-tools.properties</b>
2. 테스트에 있는 <b>@TestPropertySource</b>
3. @SpringBootTest 애노테이션의 properties attribute
4. Commandline arguments
5. <b>SPRING_APPLICATION_JSON</b>(환경변수 또는 System 프로퍼티)에 들어있는 프로퍼티
6. ServletConfig parameter
7. ServletContext parameter
8. java:comp/env JNDI attribute
9. System.getProperties() 자바 시스템 프로퍼티
10. OS 환경 변수
11. RandomValuePropertySource
12. JAR 밖에 있는 특정 프로파일용 application properties
13. JAR 안에 있는 특정 프로파일용 application properties
14. JAR 밖에 있는 application properties
15. JAR 안에 있는 application properties
16. @PropertySource
17. 기본 프로퍼티 <b>SpringApplication.setDefaultProperties</b>

<img src="/assets/img/study/ex02.png" width="70%" heigth="auto" align="center"><br/>

15번째 방법으로 작성했던 애플리케이션을 패키징한 후 실행할 때 커맨드 라인으로 다른 값을 주었더니 바뀐 값이 나온 것을 볼 수 있다. 이 방법은 4순위인 커맨드 라인 아규먼트를 사용한 방법으로 15번째 방법보다 우선순위가 높다. 따라서 기존의 방법에 오버라이딩되어 실행된 것이다.

&nbsp;&nbsp;&nbsp;2, 3번째 우선순위에 있는 방법은 테스트용 Properties를 정의하는 방법이다. 이런 모든 프로퍼티들은 기본적으로 `Environment`를 통해서 확인할 수 있는데, 스프링에 있는 `Environment`를 가져와야 한다.

```java
package me.gracenam.springinit;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.core.env.Environment;

import static org.assertj.core.api.Assertions.assertThat;

@SpringBootTest
class SpringinitApplicationTests {

    @Autowired
    Environment environment;

    @Test
    public void contextLoads() {
        assertThat(environment.getProperty("grace.name"))
                .isEqualTo("grace");
    }

}
```

`isEqualTo()`의 값이 grace인 이유는 src/main/resources 안에 있는 application.properties에 정의된 값이 grace이기 때문이다. 이제 값을 확인해 보면 정상적으로 출력되는 것을 확인할 수 있다.

<img src="/assets/img/study/ex03.png" width="70%" heigth="auto" align="center"><br/>

&nbsp;&nbsp;&nbsp;그.런.데. 이 테스트에서 사용하는 properties 파일을 바꿔야 하는 경우가 있을 수 있다. 이런 경우에 test 폴더 아래에 resources 디렉토리를 만들어 안에 application.properties 파일을 만들어 준 뒤 값을 지정해 줄 수 있다. 이 방법은 2, 3번째 우선순위 방법과는 조금 다른 방법이다.

<img src="/assets/img/study/ex04.png" width="50%" height="auto" align="center"><br/>

```
grace.name = gracenam
```

```java
package me.gracenam.springinit;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.core.env.Environment;

import static org.assertj.core.api.Assertions.assertThat;

@SpringBootTest
class SpringinitApplicationTests {

    @Autowired
    Environment environment;

    @Test
    public void contextLoads() {
        assertThat(environment.getProperty("grace.name"))
                .isEqualTo("gracenam");
    }

}
```

이렇게 한 뒤에 실행을 하면 바뀐 값이 찍히는 것을 확인할 수 있다.

<img src="/assets/img/study/ex05.png" width="70%" heigth="auto" align="center"><br/>

테스트용 빌드를 수행할 때 src에 들어 있는 파일들을 모두 classpath에 넣는다. 이 때 main 아래에 있는 모든 파일들이 claspath에 먼저 들어가서 빌드가 되고 그 다음에 test 아래에 있는 것들이 들어가게 된다. 이 과정에서 appliction.properties는 바뀌게 되고 따라서 바뀐 값이 출력되게 되는 것이다.

하지만 이렇게 사용할 경우 main에 존재하는 application.properties가 변경될 때 마다 test의 properties도 함께 변경해줘야 하는 번거로움이 있다.

&nbsp;&nbsp;&nbsp;2번째와 3번째 방법은 아래의 코드와 함께 살펴보자.

```java
package me.gracenam.springinit;

import org.junit.jupiter.api.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.core.env.Environment;
import org.springframework.test.context.TestPropertySource;
import org.springframework.test.context.junit4.SpringRunner;

import static org.assertj.core.api.Assertions.assertThat;


@RunWith(SpringRunner.class)
@TestPropertySource(properties = "grace.name=grace2")
@SpringBootTest(properties = "grace.name=grace3")
class SpringinitApplicationTests {

    @Autowired
    Environment environment;

    @Test
    public void contextLoads() {
        assertThat(environment.getProperty("grace.name"))
                .isEqualTo("gracenam");
    }

}
```

3순위인 @SpringBootTest에 재정의하는 방법은 코드에 적힌 것처럼 작성해주면 된다. 이 방법이 우선순위가 제일 높을 경우 출력 값은 grace3이 될 것이다.  
2순위인 @TestPropertySource는 별도의 애노테이션으로 선언해주는 방법으로 이 방법 역시 우선순위가 제일 높을 때 grace2가 출력될 것이다.

아니면 아예 로케이션을 주는 방법도 있다. properties 파일을 명시해주는 것이다.

```
grace.name = gracenam2
```

```java
package me.gracenam.springinit;

import org.junit.jupiter.api.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.core.env.Environment;
import org.springframework.test.context.TestPropertySource;
import org.springframework.test.context.junit4.SpringRunner;

import static org.assertj.core.api.Assertions.assertThat;


@RunWith(SpringRunner.class)
@TestPropertySource(locations = "classpath:/test.properties")
@SpringBootTest
class SpringinitApplicationTests {

    @Autowired
    Environment environment;

    @Test
    public void contextLoads() {
        assertThat(environment.getProperty("grace.name"))
                .isEqualTo("gracenam");
    }

}
```

test.properties라는 파일을 생성하고 해당 파일의 로케이션을 지정해줬다. 이렇게하면 기존의 properties 파일에서 test.properties에 적힌 값으로 덮어 씌워진다. 이 때 위에서 봤던 application.properties를 복사하는 방법과 다른 점은 test.properties에 작성된 값에만 영향을 준다는 점이다.

&#9654; application.properties 우선 순위

&nbsp;&nbsp;&nbsp;application.properties는 여러 곳에 파일을 생성할 수 있는데 이 파일의 위치에 따라서 우선 순위가 달라진다.

1. file:./config/
2. file:./
3. classpath:/config/
4. classpath:/

---
**Reference**
+ [스프링 부트 개념과 활용](https://inf.run/Xny5)
+ [공식 문서](https://docs.spring.io/spring-boot/docs/2.0.3.RELEASE/reference/htmlsingle/)
