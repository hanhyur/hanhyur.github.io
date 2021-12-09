---
layout: post
date: 2021-12-09 20:23:00
title: "HTTP 기본"
description: "모든 개발자를 위한 HTTP 웹 기본 지식"
subject: CS
category: [ cs ]
tags: [ network, internet ]
use_math: true
comments: true
---

김영한님의 [모든 개발자를 위한 HTTP 웹 기본 지식](https://inf.run/J7xC)을 공부하고 정리하는 포스트입니다.

---

# HTTP 기본

## 모든 것이 HTTP

HTTP는 HyperText Transfer Protocol로 HTML을 전송하는 프로토콜로 시작되었다. 지금은 HTML, 텍스트 뿐만 아니라 이미지, 음성, 영상, 파일, JSON, XML 등등 거의 모든 형태의 데이터를 전송할 수 있다. 심지어 서버 간에 데이터를 주고 받을 때도 대부분 HTTP를 사용한다.

### HTTP 역사

- HTTP/0.9 : 1991년. GET 메서드만 지원, HTTP 헤더 X.
- HTTP/1.0 : 1996년. 메서드, 헤더 추가.
- HTTP/1.1 : 1997년. 가장 많이 사용. 우리에게 가장 중요한 버전!
  - RFC2068 (1997) -> RFC2616 (1999) -> RFC7230 ~ 7235 (2014)
- HTTP/2 : 2015년. 성능 개선.
- HTTP/3 : 진행중. TCP 대신 UDP 사용, 성능 개선.

HTTP/1.1에 대해서 공부하는 것이 가장 중요하다.

### 기반 프로토콜

HTTP/1.1, HTTP2의 경우 TCP 프로토콜 위에서 동작을 한다. HTTP/3는 UDP 기반으로 개발이 되어있는데, 왜냐하면 TCP는 3 way라던지, 많은 데이터로 인해 느린 속도 등의 문제로 성능을 최적화 시켜 사용하기 위해 UDP 위에서 개발되고 있다.  
현재는 HTTP/1.1이 주로 사용되며 HTTP/2와 HTTP/3은 성능을 개선한 것으로 조금씩 증가하고 있다.

<img src="/assets/img/http/http08.png" width="70%" align="center"><br/>

구글에서 검색을 한 후 개발자 도구에서 protocol을 살펴보면 h2 혹은 h3를 볼 수 있다. 이 것들이 바로 HTTP/2와 HTTP/3이다. 다른 사이트에서도 확인해보면 어떤 HTTP를 사용하고 있는지 볼 수 있다.

## HTTP 특징

HTTP에는 중요한 특징들이 있는데, 먼저 HTTP는 클라이언트 서버 구조로 동작하게 되어있다. 그리고 무상태 프로토콜, Stateless라고하는 것을 지향하고 비연결성이라는 특징을 가지고 있다.  
HTTP 메세지를 통해서 주고 받고 단순하고 확장이 가능하다.

---

## 클라이언트 서버 구조

