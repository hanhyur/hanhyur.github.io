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

## HTTP API 데이터 전송

어떠한 애플리케이션이 있고 클라이언트에서 서버로 데이터를 바로 전송해야 할 때 HTTP API로 데이터를 전송한다고 말합니다. 방법은 간단합니다. 직접 메시지를 만들어서 전달하면 됩니다.

<img src="/assets/img/http/http24.png" width="70%" align="center"><br/>

이러한 방식은 주로 서버 to 서버에서 사용합니다. 백엔드 시스템에서 통신을 할 때는 HTML이 없기 때문에 이 방식을 사용합니다. 앱 클라이언트나 웹 클라이언트에서도 이 방식을 사용합니다.  
웹 클라이언트의 경우 HTML에서 Form 전송 대신 자바 스크립트를 통해서 통신하는 Ajax라는 것을 사용합니다. 그래서 요즘은 React, VueJS와 같은 웹 클라이언트들과 통신을 할 때 API 통신을 사용합니다.

HTTP API는 POST, PUT, PATCH를 사용해서 메시지 바디를 통해 데이터를 전송합니다. 물론 GET도 사용할 수 있습니다. 조회할 때 사용할 수 있고 쿼리 파라미터로 데이터를 전달합니다.

Content-Type은 주로 application/json을 사용합니다. 과거에는 XML을 사용했었지만, 읽기 어렵고 데이터도 복잡하여 최근에는 JSON을 많이 사용하게 되었습니다.

---

## HTTP API 설계 예시

다음 3가지 설계 예시를 통해서 HTTP API 설계에 대해서 알아봅시다.

- HTTP API 컬렉션
  - POST 기반 등록
  - 회원 관리 API 제공
- HTTP API 스토어
  - PUT 기반 등록
  - 정적 컨텐츠 관리, 원격 파일 관리
- HTML FORM 사용
  - 웹 페이지 회원 관리
  - GET, POST만 지원

## 회원 관리 시스템

회원 관리 시스템 예시를 살펴보겠습니다. POST 기반으로 등록하는 API 설계는 다음과 같습니다. URI를 작성할 때 리소스를 식별해야 한다는 것을 기억합시다.

- 회원 목록 /members -> GET
- 회원 등록 /members -> POST
- 회원 조회 /members/{id} -> GET
- 회원 수정 /members/{id} -> PATCH, PUT, POST
- 회원 삭제 /members/{id} -> DELETE

POST로 등록할 때 클라이언트는 등록될 리소스의 URI를 모릅니다. 즉, 식별자를 서버에서 생성해줍니다. 서버에서 새롭게 등록된 리소스 URI를 결정하고 만들어서 클라이언트에게 알려줍니다.

이러한 형식을 컬렉션(collection)이라고 합니다. 서버가 리소스의 URI를 생성하고 관리하는 것, 이 예제에서는 /members가 컬렉션입니다.

## 파일 관리 시스템

PUT 기반의 예시를 살펴봅시다. 원격지에 파일을 관리하는 시스템이 있을 때 어떻게 설계하는지 알아보겠습니다.

- 파일 목록 /files -> GET
- 파일 조회 /files/{filename} -> GET
- 파일 등록 /files/{filename} -> PUT
- 파일 삭제 /files/{filename} -> DELETE
- 파일 대량 등록 /files -> POST

흔히 우리가 파일을 등록할 때 기존 파일을 덮어쓰거나 대체합니다. 그렇기 때문에 PUT이 적절하다고 할 수 있습니다.  

파일을 등록할 때 PUT을 사용하고 있기 때문에 POST의 의미를 임의로 정할 수 있습니다. 이 예제에서는 대량 등록이라고 임의로 지정했습니다. 물론 다른 경로로 사용해도 됩니다.

POST와는 달리 PUT은 클라이언트가 리소스 URI를 알고 있어야 합니다. 즉 식별하는 사람이 클라이언트라는 의미입니다. 클라이언트가 직접 리소스의 URI를 지정하고 전달합니다.

이러한 형식을 스토어(store)라고 합니다. 클라이언트가 리소스의 URI를 알고 관리하는 것이고 이 예제에서는 /files가 스토어입니다.

## HTML FORM 사용

HTML FORM은 기본적으로 GET, POST만 지원합니다. 물론 AJAX와 같은 기술을 사용해서 해결할 수 있지만 여기서는 순수 HTML FORM을 가지고 알아 보겠습니다.

- 회원 목록 /members -> GET
- 회원 등록 폼 /members/new -> GET
- 회원 등록 /members/new, /members -> POST
- 회원 조회 /members/{id} -> GET
- 회원 수정 폼 /members/{id}/edit -> GET
- 회원 수정 /members/{id}/edit, /members/{id} -> POST
- 회원 삭제 /members/{id}/delete -> POST

일부 API의 경우 2가지가 있는데, GET과 POST의 차이만 두고 같은 URI를 사용하는 것을 추천합니다. URI 경로 자체가 이동해버리면 다시 FORM으로 돌아갈 수 없기 때문에 애매할 수 있습니다.

HTML FORM은 GET, POST만 지원하기 때문에 컨트롤 URI라는 것을 사용해야 합니다. 컨트롤 URI는 동사로 된 리소스 경로로 /new, /edit, /delete가 컨트롤 URI입니다. HTTP 메서드로 해결하기 애매한 경우에 사용하는 것으로 실무에서 굉장히 많이 사용됩니다. 단, 아무때나 사용하는 것이 아니라 최대한 리소스라는 개념을 가지고 설계를 하다가 안될 때 대체제로 사용해야 합니다.

---

## 정리

- HTTP API 컬렉션
  - POST 기반 등록
  - 서버가 리소스 URI 결정
- HTTP API 스토어
  - PUT 기반 등록
  - 클라이언트가 리소스 URI 결정
- HTML FORM 사용
  - 순수 HTML + HTML FORM 사용
  - GET, POST만 지원

### 참고하면 좋은 URI 설계 개념

- 문서 (document)
  - 단일 개념(파일 하나, 객체 인스턴스, 데이터베이스 row)
  - /members/100, /files/star.jpg
- 컬렉션 (collection)
  - 서버가 관리하는 리소스 디렉터리
  - 서버가 리소스의 URI를 생성하고 관리
  - /members
- 스토어 (store)
  - 클라이언트가 관리하는 자원 저장소
  - 클라이언트가 리소스의 URI를 식별하고 관리
  - /files
- 컨트롤러 (controller), 컨트롤 URI
  - 문서, 컬렉션, 스토어로 해결하기 어려운 추가 프로세스 실행
  - 동사를 직접 사용
  - /members/{id}/delete