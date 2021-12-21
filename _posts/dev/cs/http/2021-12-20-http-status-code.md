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

조금 더 복잡한 3xx 시리즈인 리다이렉션에 대해서 알아보겠습니다. 리다이렉션은 요청을 완료하기 위해 유저 에이전트(클라이언트 프로그램, 주로 웹브라우저)의 추가적인 작업이 필요하다고 다시 보내는 것입니다.

- 300 Multiple Choices
- 301 Moved Permanently
- 302 Found
- 303 See Other
- 304 Not Modified
- 307 Temporary Redirect
- 308 Permanent Redirect

3xx 시리즈는 300번 Multiple Choices는 거의 사용되지 않고, 301 부터 308 까지가 중요합니다.

가장 중요한 리다이렉션에 대해서 이해해야 합니다. 웹 브라우저는 3xx 응답의 결과에 Location 헤더가 있으면, Location 위치로 자동으로 이동합니다. 이것을 리다이렉트 라고 이야기합니다. 아래 그림은 자동 리다이렉션의 흐름을 나타낸 것입니다.

<img src="/assets/img/http/http27.png" width="70%" align="center"><br/>

## 리다이렉션 종류

리다이렉션에는 3가지 종류가 있습니다.

- 영구 리다이렉션 : 특정 리소스의 URI가 영구적으로 이동
  - 예) /members -> /users
- 일시 리다이렉션 : 일시적인 변경
  - 주문 완료 후 주문 내역 화면으로 이동
  - PRG: POST / Redirect / Get
- 특수 리다이렉션
  - 결과 대신 캐시를 사용

## 영구 리다이렉션

301과 308이 영구 리다이렉션에 해당합니다. 리소스의 URI가 영구적으로 이동했다는 것을 알려주는데 원래 URL을 더 이상 사용해서는 안된다고 알려줘야 합니다. 이러한 변화에 대해서 검색 엔진 등에서도 인지 할 수 있습니다.

301과 308 둘 다 경로가 변경되었음을 알려주는데 차이점이 무엇일까요? 301의 경우 리다이렉트 시 요청 메서드가 GET으로 변하고, 본문이 제거될 수도 있습니다. 308은 301과 기능이 동일하지만, 리다이렉트 시 요청 메서드와 본문을 유지합니다. 처음 POST를 보내면 리다이렉트도 POST를 유지합니다.

<img src="/assets/img/http/http28.png" width="70%" align="center"><br/>

등록을 위해 POST로 보냈지만 결과적으로 GET으로 전송되고 메시지 바디 부분이 사라질 수 있습니다. 이러한 부분을 308로 해결할 수 있습니다.

<img src="/assets/img/http/http29.png" width="70%" align="center"><br/>

## 일시적 리다이렉션
