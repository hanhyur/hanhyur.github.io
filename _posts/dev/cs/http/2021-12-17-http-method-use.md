---
layout: post
date: 2021-12-17 21:44:00
title: "HTTP 메서드 활용"
description: "모든 개발자를 위한 HTTP 웹 기본 지식"
subject: CS
category: [ cs ]
tags: [ network, internet ]
use_math: true
comments: true
---

김영한님의 [모든 개발자를 위한 HTTP 웹 기본 지식](https://inf.run/J7xC)을 공부하고 정리하는 포스트입니다.

---

# HTTP 메서드 활용

지금까지 HTTP 메서드에서 알아봤습니다. 지금까지 배운 내용이 개발할 때 어떻게 활용되는지 알아봅시다.

## 클라이언트에서 서버로 데이터 전송

데이터를 전달하는 방식은 크게 2가지로 나눌 수 있습니다. 하나는 쿼리 파라미터를 통한 데이터 전송이고, 하나는 메시지 바디를 통한 데이터 전송입니다.

### 쿼리 파라미터를 통한 데이터 전송

? 뒤에 key-value를 이용해 URI 끝에 쿼리 파라미터를 추가하여 전송하는 방식입니다. GET에서 많이 사용하고 주로 정렬 필터(검색어)와 같은 경우에 많이 사용됩니다.

### 메시지 바디를 통한 데이터 전송

HTTP 메시지 바디를 통해 데이터를 전송하는 방식은 POST, PUT, PATCH를 사용합니다. 주로 회원 가입, 상품 주문, 리소스 등록, 리소스 변경할 때 사용합니다.

---

클라이언트에서 서버로 데이터 전송하는 상황을 아래 4가지 예시를 통해서 알아봅시다.

- 정적 데이터 조회
  - 이미지, 정적 텍스트 문서
- 동적 데이터 조회
  - 주로 검색, 게시판 목록에서 정렬 필터(검색어)
- HTLM Form을 통한 데이터 전송
  - 회원가입, 상품주문, 데이터 변경
- HTTP API를 통한 데이터 전송
  - 회원가입, 상품주문, 데이터 변경
  - 서버 to 서버, 앱 클라이언트, 웹 클라이언트(Ajax)

## 정적 데이터 조회

먼저 정적 데이터를 조회하는 것입니다.

<img src="/assets/img/http/http19.png" width="70%" align="center"><br/>

이미지를 조회할 경우 단순히 이미지 리소스를 만들어서 클라이언트에서 내려줍니다. 쿼리 파라미터가 필요 없습니다.

이처럼 이미지나 정적 텍스트 문서를 조회할 때는 GET을 사용하며 일반적으로 쿼리 파라미터 없이 리소스 경로만으로 조회가 가능합니다.

## 동적 데이터 조회

<img src="/assets/img/http/http20.png" width="70%" align="center"><br/>

조회를 할 때 데이터를 전달해야 할 때가 있는데 바로 동적 데이터를 조회할 때 입니다. 지금까지 우리는 구글에서 검색할 때 쿼리 파라미터를 사용했었습니다.  
이렇게 쿼리 파라미터를 통해 데이터를 전달하면 서버에서는 쿼리 파라미터를 기반으로 정렬 필터해서 결과를 동적으로 생성하게 됩니다.

주로 검색, 게시판 목록에서 조회 조건을 줄여주는 필터(검색어), 조회 결과를 정렬하는 정렬 조건에 사용됩니다. 조회이기 때문에 GET을 사용하며 쿼리 파라미터로 데이터가 전달됩니다.

## HTML Form 데이터 전송

이번에는 HTML Form을 가지고 데이터를 전송하는 예시를 살펴봅시다.

<img src="/assets/img/http/http21.png" width="70%" align="center"><br/>

submit 버튼을 누르게 되면 웹 브라우저가 form의 데이터를 읽어서 메시지를 생성해줍니다. 쿼리 파라미터의 key-value와 같은 형식으로 만들어서 메시지 바디에 담아서 전달합니다.

여기서 한 가지 기억해야할 점이 있습니다. Form 데이터를 사용할 때 method의 형식을 get으로 바꿀 수 있습니다. get으로 바꾸면 어떻게 될까요? 데이터를 메시지 바디가 아닌 쿼리 파라미터에 추가해서 서버에 전달하게 됩니다.

<img src="/assets/img/http/http22.png" width="70%" align="center"><br/>

추가로 HTML Form에서 파일을 전송할 때 사용하는 것이 있습니다. `multipart/form-data`라는 것인데 content-type에 이어서 boundary라는 것이 추가됩니다. 이를 통해서 데이터를 잘라서 메시지를 작성합니다. 즉 여러 타입의 데이터를 전송할 수 있게 해줍니다. 주로 바이너리 데이터를 전송할 때 사용됩니다.

<img src="/assets/img/http/http23.png" width="70%" align="center"><br/>

### 정리

- HTML Form submit시 POST 전송
  - 예) 회원가입, 상품주문, 데이터 변경
- Content-Type : application/x-www-form-urlencoded 사용
  - form의 내용을 메시지 바디를 통해서 전송(key-value, 쿼리 파라미터 형식)
  - 전송 데이터를 url encoding 처리
- HTML Form은 GET 전송도 가능
- Content-Type : multipart/form-data
  - 파일 업로드 같은 바이너리 데이터 전송 시 사용
  - 다른 종류의 여러 파일과 폼의 내용 함께 전송 가능(multipart의 의미)

참고로 HTML Form 전송은 GET과 POST만 지원합니다.

## HTTP API를 통한 데이터 전송

