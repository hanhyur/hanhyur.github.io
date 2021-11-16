---
layout: post
date: 2021-11-05 00:51:00
title: "스프링 웹 계층이란?"
description: "스프링 MVC"
subject: Spring
category: [ spring ]
tags: [ spring, web, layer ]
use_math: true
comments: true
---

# 스프링 웹 계층

&nbsp;&nbsp;&nbsp;스프링을 배운 이후 스프링 프로젝트를 진행하던 중 분명히 배운 내용임에도 불구하고 기억하지 못하는 경우가 많이 있다. 그래서 이 기회에 기초부터 다시 싹 정리할 예정이다.  

스프링은 어떤 계층이 존재하는지와 계층의 역할을 무엇인지, 프로젝트 시 패키지를 어떻게 나누는 것이 좋은지에 대해 정리해보려 한다.

<img src="https://blog.kakaocdn.net/dn/bRKzjn/btqHoqiwtuG/km0VKjT5YI65M6kjyiQVV1/img.png" width="70%" height="auto" align="center"><center><출처 : https://devlog-wjdrbs96.tistory.com/209></center>

&nbsp;&nbsp;&nbsp;스프링의 계층은 크게 3가지 

+ Presentation Layer
+ Service Layer(Business Layer)
+ Data Access Layer(Repository Layer)

로 나눌 수 있다. 이제 각 계층에 대해서 살펴보자.

## Presentation Layer

&nbsp;&nbsp;&nbsp;Presentation Layer는 브라우저상의 웹 클라이언트의 요청 및 응답을 처리하는 레이어이다. 서비스계층, 데이터 엑세스 계층에서 발생하는 Exception을 처리해주며 `@Controller` 애노테이션을 사용하여 작성된 Controller 클래스가 이 계층에 속한다.

Controller와 View로 구성되어 있으며 기능을 살펴보면 공통적인 URI경로, 각 기능별 URI 지정해주고 결과 처리, 페이지 이동, 예외처리 등을 할 수 있다. Controller는 모듈로서 특정 메뉴, 기능묶음 단위로 생성하게 되는데 URI를 어떤 방식으로 처리할 것인지에 대한 설계가 필요하다<sup>[1](#footnote_1)</sup>.

## Service Layer

&nbsp;&nbsp;&nbsp;Service Layer 또는 Business Layer라고 불리는 이 계층은 사용자의 요구사항을 일치시켜서 구현하는 계층으로 프레젠테이션 계층과 데이터 엑세스 계층 사이를 연결하는 역할로서 두 계층이 직접적으로 통신하지 않게 한다. 쉽게 말해서 DAO와 Controller 사이를 연결해준다.

이 부분은 고객마다 다르게 처리해야하는 부분을 처리하는데 DB와 무관하게 처리할 수 있는 영역으로 컨트롤러(외부 호출)의 영속계층(persistence)종속을 막아준다. 컨트롤러가 트렌젝션, 예외처리 등을 모두 처리해야하지만 종속적인 상황을 벗어나면 컨트롤러는 컨트롤러의 역할만 할 수 있다. 예를 들어 우리(Controller)가 은행을 사용하는데 은행원(Service)이 어떤 처리를 거쳐서 우리에게 입금해주는지 알 필요가 없는 것과 같다. 또한 애플리케이션 비즈니스 로직 처리와 비즈니스와 관련된 도메인 모델의 적합성 검증하고 트랜잭션을 관리한다.

`Service` 인터페이스와 `@Service` 애노테이션을 사용하여 작성된 Service 구현 클래스가 이 계층에 속하는데 `@Service`는 해당 클래스를 서비스 객체로 처리(스프링에서 인식할 수 있게 만듬)하라는 의미이다.

## Data Access Layer

&nbsp;&nbsp;&nbsp;Repository Layer라고도 불리는 이 계층은 ORM(Mybatis, Hibernate)를 주로 사용하는 계층으로 DAO 인터페이스와 `@Repository` 애노테이션을 사용하여 작성된 DAO 구현 클래스가 이 계층에 속한다. Database에 Data를 CRUD(Create, Read, Update, Drop)하는 계층이기도 하다.

---

&nbsp;&nbsp;&nbsp;각 계층을 간략하게 살펴보았는데, 중복되거나 다소 헷갈리는 용어들을 정리해보자.

### DTO(Data Tranfer Object)

- 각 계층간 데이터 교환을 위한 객체 (데이터를 주고 받을 포맷)
- Domain, VO라고도 부름
- DB에서 데이터를 얻어 Service, Controller 등으로 보낼 때 사용함
- 로직을 갖지 않고 순수하게 getter, setter 메소드를 가진다.

### DAO(Data Access Object)

- DB에 접근하는 객체, DB를 사용해 데이터를 조작하는 기능을 하는 객체 (MyBatis 사용시에 DAO or Mapper 사용)
- Repository라고도 부름(JPA 사용시 Repository 사용)
-Service 계층과 DB를 연결하는 고리 역할을 한다.
 

### Entity 클래스란?

- Domain 이라고도 부름 (JPA 사용할 때 사용)
- 실제 DB 테이블과 매칭될 클래스
- Entity 클래스 또는 가장 Core한 클래스라고 부름
- Domain 로직만을 가지고 있어야하며 Presentation Logic을 가지고 있어서는 안된다.

### Domain 클래스와 DTO 클래스를 분리하는 이유

- View Layer와 DB Layer의 역할을 철저하게 분리하기 위해서
- 테이블과 매핑되는 Entity 클래스가 변경되면 여러 클래스에 영향을 끼치게 되지만 View와 통신하는 DTO 클래스는 자주 변경되므로 분리해야 한다.
- 즉 DTO는 Domain Model을 복사한 형태로, 다양한 Presentation Logic을 추가한 정도로 사용하며 Domain Model 객체는 Persistent만을 위해서 사용한다.

<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fb9y2vv%2FbtqG7TmUzrp%2FKq6rV9U1j3dUp2wtbWXVT1%2Fimg.png" width="70%" height="auto" align="center"><center><출처 : https://gmlwjd9405.github.io/2018/12/25/difference-dao-dto-entity.html></center>

<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdZGdFD%2FbtqHjX9g9Hr%2F85ZSOknN2TOiwR3kqonCM0%2Fimg.png" width="70%" height="auto" align="center"><center><출처 : https://devlog-wjdrbs96.tistory.com/209></center>

&nbsp;&nbsp;&nbsp;위의 그림처럼 각 계층이 나눠져 있고 DTO, DAO를 통해서 설계를 하는 것을 알 수 있다.

### RestController와 @Service를 나누는 이유

@Service를 만들어서 나누는 이유는 중복되는 코드가 생기기 때문이다. 비즈니스 로직은 다른 요청 URL에서 사용해야 하는 경우가 있다. 만약 비즈니스 로직 코드가 컨트롤러에 구현되어 있는 경우, 다른 컨트롤러의 핸들러 메소드에서 똑같은 로직코드를 구현해야하니 중복 코드가 발생하고 재사용성이 줄어든다.  
중복되는 코드가 발생하면 따로 모듈화를 해서 나눠주는 것이 유지 보수하기 편리하다. (NodeJS => 모듈화)
모든 기능들을 세분화해서 `@Service`에 작성하게 되면 나중에는 서비스의 기능들을 조합만 해서 새로운 기능을 만들 수 있고 서비스에서 다른 서비스를 의존성 참조하는 것도 가능하다.

즉, 비즈니스 로직의 서비스 구현체에서 구현하는 이유는 바로 확장성과 재사용성 그리고 중복 코드의 제거이다. 

---
**Reference**

- <https://devlog-wjdrbs96.tistory.com/209>
- <https://sowon-dev.github.io/2020/10/18/201019spring/>

---
<a name="footnote_1">1</a> : URI 처리 방식에 대해서는 REST API를 정리하는 글에서 다시 다뤄보겠다.