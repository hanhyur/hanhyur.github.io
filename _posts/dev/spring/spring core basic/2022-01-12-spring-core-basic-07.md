---
layout: post
date: 2022-01-08 12:22:00
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

