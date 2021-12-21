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

<img src="/assets/img/http/http25.png" width="70%" align="center"><br/>

200 OK는 가장 많이 보는 것입니다. 리소스를 요청하면 서버에서 결과가 정상적으로 잘 처리해서 응답하면 200 OK가 나옵니다.

## 201 Created

<img src="/assets/img/http/http26.png" width="70%" align="center"><br/>

간혹 사용되는 것으로 요청에 성공하면 새로운 리소스가 생성됩니다. 클라이언트가 POST로 요청을 하면 서버에서 자원을 생성하고 URI를 관리해줍니다.

따라서 201이 넘어오면 요청이 성공했고 Location Header가 있을 수 있겠다고 판단할 수 있습니다.

## 202 Accepted

요청이 접수는 되었지만 처리가 되지 않은 경우 나타납니다. 예를 들어 1시간 뒤에 배치 프로세스가 요청을 처리하는 경우가 있습니다.

## 204 No Content

서버가 요청을 성공적으로 수행했지만, 응답으로 보낼 데이터가 없는 경우 발생합니다. 예를 들어 게시글을 저장할 때 사용할 수 있습니다. 본문을 작성하고 저장을 누른 다음 같은 화면을 유지하면 되기 때문에 사용할 수 있습니다.

결과 내용이 없어도 204 메시지로 성공을 인식할 수 있습니다.

---

# 3xx - 리다이렉션

