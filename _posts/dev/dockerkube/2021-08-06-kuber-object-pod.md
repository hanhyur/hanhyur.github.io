---
layout: post
date: 2021-08-06 22:00:00
title: "쿠버네티스 기본 오브젝트 - Pod"
description: "Kubernetes"
subject: Kubernetes
category: [ kubernetes ]
tags: [ kubernetes, object, pod ]
use_math: true
comments: true
---

# Pod

&nbsp;&nbsp;&nbsp;<b>파드(Pod)[^1]</b>는 쿠버네티스 오브젝트 중 하나로, 하나 이상의 컨테이너 그룹으로 구성되어 있는 쿠버네티스 애플리케이션의 가장 작은 컴퓨팅 단위이다. 파드 안 컨테이너들은 그룹 단위로 스케쥴되어 함께 실행되고 중지되어야 하고, 동일한 물리 머신에서 실행되어야 한다.

대부분 고급 애플리케이션은 하나의 파드에 여러 개의 컨테이너가 들어있지만, 단일 컨테이너로 이루어진 파드도 있다. 이러한 파드를 만드는 이유는 리소스를 보다 효율적으로 사용하기 위해서이다.

&nbsp;&nbsp;&nbsp;쿠버네티스는 컨테이너를 직접 관리하지 않는다. 대신 파드를 실행하면서 파드 속의 컨테이너들이 동일한 리소스와 네틑워크를 공유하게 된다. 

## Container

&nbsp;&nbsp;&nbsp;

---
[^1] : 파드는 고래의 무리를 의미한다고 한다.