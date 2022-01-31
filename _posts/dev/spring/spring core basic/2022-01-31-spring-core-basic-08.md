---
layout: post
date: 2022-01-31 22:04:00
title: "스프링 컨테이너와 스프링 빈 2부"
description: "스프링 핵심 원리 - 기본편"
subject: Spring
category: [ spring ]
tags: [ spring, basic, oop ]
use_math: true
comments: true
---

[스프링 핵심 원리 - 기본편](https://inf.run/bMZx)을 공부하고 정리하는 포스트입니다.

---

## 스프링 빈 조회 - 상속 관계

빈을 조회할 때 상속 관계로 되어 있어서 부모 타입으로 조회하면 어떻게 될까요? 자식 타입도 함께 조회됩니다. 이게 스프링 빈 조회의 기본 대원칙입니다.

따라서 모든 자바 객체의 부모 타입인 `Object`를 조회하면, 모든 스프링 빈이 조회됩니다.

