---
layout: post
date: 2021-12-20 23:00:00
title: "HTTP 상태코드"
description: "모든 개발자를 위한 HTTP 웹 기본 지식"
subject: CS
category: [ cs ]
tags: [ network, internet ]
use_math: true
comments: true
---

김영한님의 [모든 개발자를 위한 HTTP 웹 기본 지식](https://inf.run/J7xC)을 공부하고 정리하는 포스트입니다.

---

# HTTP 상태코드

HTTP 상태코드에 대해서 알아보겠습니다. 상태코드는 클라이언트가 서버에 요청을 보냈을 때, 요청이 잘 처리되었는지 응답이 올 때(Response) 알려주는 코드입니다.

- 1xx (Informational): 요청이 수신되어 처리중
- 2xx (Successful): 요청 정상 처리
- 3xx (Redirection): 요청을 완료하려면 추가 행동이 필요
- 4xx (Client Error): 클라이언트 오류, 잘못된 문법등으로 서버가 요청을 수행할 수 없음
- 5xx (Server Error): 서버 오류, 서버가 정상 요청을 처리하지 못함

만약 미래에 추가적인 코드가 추가되어서 모르는 상태의 코드가 나타난다면 어떻게 해야할까요? 클라이언트가 인식할 수 없는 코드가 오더라도 위에 정의가 된 것처럼 맨 앞자리를 보고 판단하면 됩니다.

---

# 2xx - 성공

2xx 시리즈는 성공을 했다고 생각하면 됩니다.

- 200 OK
- 201 Created
- 202 Accepted
- 204 No Content

하나씩 살펴보겠습니다.

## 200 OK

