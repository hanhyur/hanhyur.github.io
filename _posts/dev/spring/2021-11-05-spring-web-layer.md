---
layout: post
date: 2021-11-05 00:51:00
title: "스프링 웹 계층이란?"
description: "스프링 MVC"
subject: Spring / Spring MVC
category: [ spring ]
tags: [ spring, web, layer ]
use_math: true
comments: true
---

# 스프링 웹 계층

&nbsp;&nbsp;&nbsp;스프링을 배운 이후 스프링 프로젝트를 진행하던 중 분명히 배운 내용임에도 불구하고 기억하지 못하는 경우가 많이 있다. 그래서 이 기회에 기초부터 다시 싹 정리할 예정이다.  

스프링은 어떤 계층이 존재하는지와 계층의 역할을 무엇인지, 프로젝트 시 패키지를 어떻게 나누는 것이 좋은지에 대해 정리해보려 한다.

<img src="https://blog.kakaocdn.net/dn/bRKzjn/btqHoqiwtuG/km0VKjT5YI65M6kjyiQVV1/img.png" width="70%" height="auto" align="center"><br/><center><출처 : https://devlog-wjdrbs96.tistory.com/209></center>

&nbsp;&nbsp;&nbsp;스프링의 계층은 크게 3가지 

+ Presentation Layer
+ Service Layer(Business Layer)
+ Data Access Layer(Repository Layer)

로 나눌 수 있다. 이제 각 계층에 대해서 살펴보자.

## Presentation Layer

&nbsp;&nbsp;&nbsp;Presentation Layer는 브라우저상의 웹 클라이언트의 요청 및 응답을 처리하는 레이어이다. 서비스계층, 데이터 엑세스 계층에서 발생하는 Exception을 처리해주며 `@Controller` 어노테이션을 사용하여 작성된 Controller 클래스가 이 계층에 속한다.

Controller와 View로 구성되어 있으며 기능을 살펴보면 공통적인 URI경로, 각 기능별 URI 지정해주고 결과 처리, 페이지 이동, 예외처리 등을 할 수 있다. Controller는 모듈로서 특정 메뉴, 기능묶음 단위로 생성하게 되는데 URI를 어떤 방식으로 처리할 것인지에 대한 설계가 필요하다.