---
layout: post
date: 2022-02-25 19:00:00
title: "컴포넌트 스캔"
description: "스프링 핵심 원리 - 기본편"
subject: Spring
category: [ spring ]
tags: [ spring, basic, oop ]
use_math: true
comments: true
---

[스프링 핵심 원리 - 기본편](https://inf.run/bMZx)을 공부하고 정리하는 포스트입니다.

---

# 컴포넌트 스캔

컴포넌트 스캔이랑 의존관계 자동 주입은 서로 연관 관계가 있습니다. 이 둘의 연관관계를 먼저 알아보겠습니다.

## 컴포넌트 스캔과 의존관계 자동 주입 시작하기

지금까지 스프링 빈을 등록할 때는 자바 코드의 `@Bean`이나 XML의 `<bean>` 등을 통해서 설정 정보에 직접 등록할 스프링 빈을 나열했습니다.  
간단한 예제에서는 개수가 적어서 괜찮았지만, 실무에서는 무수히 많은 스프링 빈을 일일히 등록하기 힘들고, 설정 정보가 커지고, 혹은 누락되는 등의 문제가 생길 수 있습니다.

그래서 스프링은 설정 정보가 없어도 자동으로 스프링 빈을 등록하는 '컴포넌트 스캔'이라는 기능을 제공합니다. 또 의존관계도 자동으로 주입하는 `@Autowired`라는 기능도 제공합니다.

직접 코드를 통해서 알아보기 위해, `AutoAppConfig.java`를 만들어줍니다.

```java
package hello.core;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.FilterType;

@Configuration
@ComponentScan(
    excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class)
)
public class AutoAppConfig {

}
```

`@ComponentScan`은 `@Component` 애노테이션이 붙은 클래스를 찾아서 자동으로 등록해주는데 기존의 직접 등록했던 `AppConfig.class`에 있는 것을 등록하는 것을 방지하기 위해서 필터를 추가했습니다.

컴포넌트 스캔을 사용하기 위해서는 `@ComponentScan`을 붙여주면 됩니다. 수동으로 등록해주던 것과 다르게 `@Bean`으로 등록한 클래스가 없습니다.

