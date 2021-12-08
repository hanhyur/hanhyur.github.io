---
layout: post
date: 2021-12-08 21:18:00
title: "URI와 웹 브라우저 요청 흐름"
description: "모든 개발자를 위한 HTTP 웹 기본 지식"
subject: CS
category: [ cs ]
tags: [ network, internet ]
use_math: true
comments: true
---

김영한님의 [모든 개발자를 위한 HTTP 웹 기본 지식](https://inf.run/J7xC)을 공부하고 정리하는 포스트입니다.

---

# URI와 웹 브라우저 요청 흐름

## URI? URL? URN?

URI는 Uniform Resource Identifier의 약자로 리소스를 식별하는 통합된 방법 정도로 해석할 수 있다.

보통 URI나 URL은 들어봤지만 URN은 처음 듣는 사람이 많을 것이다. 이게 무엇인지 한 번 알아보자.

"URI는 로케이터(Locator), 이름(Name) 또는 둘 다 추가로 분류될 수 있다."<sup>[1](#footnote_1)</sup>

표준 스택에는 위와 같이 정의되어있다.

<img src="/assets/img/http/http04.png" width="70%" align="center"><br/>

URI라는 리소스를 식별하는 가장 큰 개념이 있다. 이 리소스를 식별하는 방법이 두 가지가 있는데, 바로 URL(Resource Locator)와 URN(Resource Name)이다. URL은 리소스가 있는 위치를 알려주는 것이고, URN은 리소스 자체의 이름이다. 

### URI

- Uniform : 리소스를 식별하는 통일된 방식
- Resource : 자원, URI로 식별할 수 있는 모든 것(제한 없음)
- Identifier : 다른 항목과 구분하는데 필요한 정보

### URL, URN

- URL Locator : 리소스가 있는 위치를 지정
- URN Name : 리소스에 이름을 부여

URN은 위치는 변할 수 있지만 이름은 변하지 않는다는 생각에서 나온 것이지만 URN 이름만으로 실제 리소스를 찾을 수 있는 방법이 보편화 되어있지 않다. 그래서 거의 사용되지 않는다.

따라서 우리는 URI와 URL만 알면 되는데, URI 내부에 URL이 있으므로 같은 의미로 생각하면 된다.

---

## URL 전체 문법

```
scheme://[userinfo@]host[:port][/path][?query][#fragment]
https://www.google.com:443/search?q=hello&hl=ko
```

URL의 문법은 다음과 같은 요소로 이루어져 있다.

- 프로토콜 : https
- 호스트명 : www.google.com
- 포트번호 : 443
- 패스 : /search
- 쿼리 파라미터 : q=hello&hl=ko

## Scheme

스키마는 주로 프로토콜이 사용된다. 프로토콜은 어떤 방식으로 자원에 접근할 것인가 하는 클라이언트와 서버 간의 약속과 규칙이라고 할 수 있다. http, https, ftp 등이 있다.  
주로 http는 80포트, https는 443 포트를 사용하는데 이 포트는 생략할 수 있다.

## userinfo

사용자 정보는 URL에 사용자 정보를 포함해서 인증할 때 사용되는데 거의 사용하지 않는다. 

## host

호스트, 호스트명은 도메인 명이나 IP 주소를 직접 입력할 수 있다.

## port

포트는 접속 포트로 일반적으로는 거의 생략한다.

## path

패스는 리소스가 있는 경로이다. 보통 계층적 구조로 이루어져 있다. ex) /members/100, /items/iphone12

## query

쿼리는 key=value의 형태로 데이터가 들어가는데, ?로 시작하고 &로 파라미터를 계속 추가할 수 있다. 공식적인 이름은 query인데 보통 query parameter, query string 등으로 불린다.

## fragment

프래그먼트는 잘 사용하지는 않는데 html 내부 북마크 등에서 사용하는 것으로 서버에 전송되는 정보는 아니다.

---

# 웹 브라우저 요청 흐름

우리가 주소를 입력하면 어떤 일이 일어나는지 알아보자.

먼저 웹 브라우저가 서버를 찾아야 하기 때문에 DNS 서버를 조회한다. 거기서 IP를 얻어오고 port 정보를 찾아낸다. 그리고 난 다음 HTTP 요청 메시지를 생성한다. HTTP 요청 메시지는 다음과 같이 형태를 가지고 있다.

<img src="/assets/img/http/http05.png" width="70%" align="center"><br/>

GET 뒤에 패스, 쿼리가 들어가고 HTTP 버전 정보와 HOST라는 정보가 들어간다. 부가정보가 더 있지만 간략하게 나타나면 위와 같은 형태를 가진다.

웹 브라우저가 HTTP 메시지를 생성하고 나면, SOCKET 라이브러리를 통해서 전달이 되는데 바로 전달되는게 아니라 먼저 TCP/IP 연결을 통해 서버와 연결을 한다. 연결된 것이 확인되면 데이터를 전달해서 패킷이 생성되고 인터넷으로 흘러가게 된다.

<img src="/assets/img/http/http06.png" width="70%" align="center"><br/>

이렇게 전달된 데이터가 서버에 도착하면 서버는 패킷을 뜯어서 버리고 안에 있는 데이터를 분석한다. 그리고 분석한 내용을 토대로 응답 메시지를 만든다.

<img src="/assets/img/http/http07.png" width="70%" align="center"><br/>

서버도 똑같은 과정을 거쳐서 클라이언트에게 응답 패킷을 보내고 패킷을 까서 HTML을 렌더링하면 그 화면이 우리에게 보이게 된다.

---
<a name="footnote_1">1</a> : <https://www.ietf.org/rfc/rfc3986.txt> - 1.1.3. URI, URL, and URN