---
layout: post
date: 2021-04-27 08:00:00
title: "스프링 AOP"
description: "스프링 프레임워크 핵심 기술"
subject: Spring framework
category: [ spring ]
tags: [ spring, IoC, AOP, Proxy, @AOP ]
use_math: true
comments: true
---

[스프링 프레임워크 핵심기술](https://www.inflearn.com/course/spring-framework_core/dashboard)을 공부하고 정리하는 포스트입니다.

---

# 스프링 AOP

&nbsp;&nbsp;&nbsp;<b>AOP</b>란 <b>Aspect-oriented Programming</b>의 약어로 스프링 AOP는 AOP의 구현체를 제공하며, 자바에 만들어져 있는 프레임워크 <b>AspectJ</b>[^1]라는 또 다른 구현체와 연동해서 사용할 수 있는 기능을 제공한다. 이러한 기능을 기반으로 스프링 트랜잭션과 같은 다른 기능이 적용되고 있다.

그렇다면 AOP는 정확히 무엇일까? AOP, 관점 지향 프로그래밍은 흩어진 Aspect를 모듈화 할 수 있는 프로그래밍 기법을 말한다. 관점 지향은 어떤 로직을 기준으로 핵심적인 관점, 부가적인 괌점으로 나누어 본다는 말이고 따라서 관점을 기준으로 각각 모듈화하는 프로그래밍 기법인 것이다. <b>OOP(Object-oriented Programming, 객체 지향 프로그래밍)</b>과 나란히 하는, 서로 보완관계에 있는 기술이다.

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



---
**Reference**
+ [스프링 프레임워크 핵심기술](https://www.inflearn.com/course/spring-framework_core/dashboard)

___
[^1]:PARC에서 개발한 자바용 AOP 확장 기능.
[^2]:Proto Bean을 감싸고 있는 Bean. 일종의 껍질.