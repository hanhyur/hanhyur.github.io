---
layout: post
date: 2021-08-31 10:35:00
title: "면접 기초 질문 리스트"
description: "백엔드 면접 대비 질문 리스트"
subject: Interview
category: [ cs ]
tags: [ cs, interview, web ]
comments: true
---

> 원 글 : 안산학생님의 [백엔드 면접 기초 질문 리스트](https://haejun0317.tistory.com/238)

&nbsp;&nbsp;&nbsp;안산학생님께서 백엔드 직무를 준비하시면서 공부하신 것들을 정리한 글을 보고 공부도 하고 기록도 하기 위해서 작성하는 글이다.

질문에 대한 답변을 스스로 해보고 각 질문을 클릭하면 해당 답변이 나타난다.

p.s. 답변에 대한 보다 자세한 내용을 알고 싶다면 자바 스터디 포스팅들을 참고하면 된다.

---

<details>
  <summary><b>컴포넌트와 모듈의 차이</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;컴포넌트와 모듈은 비슷하지만 모듈이 컴포넌트보다 큰 단위라고 할 수 있다. 두 용어 모두 전체 시스템을 구성하는 부분 부분을 분해하는 것을 목적으로 사용된다.</p>
  <p>&nbsp;&nbsp;&nbsp;컴포넌트는 하나의 부품으로, 보통 작은 영역에서 서로 연관되어 다용도로 사용이 가능하게 만든다. 컴포넌트는 런타임 개체를 참조하는데 예를 들어 UI를 제어하는 타이머와 같이 Back단에서 스레드를 보조하는 컴포넌트가 있다.  
  &nbsp;&nbsp;&nbsp;모듈은 작은 범위의 조각으로 가장 첫 번째 그리고 가장 맨 앞에 위치하는 구현의 단위이다. 외부 인터페이스가 없는 복합적인 수요기능에서 실행될 수 있는 단위로 호환성이 좋다. 예시로 데이터베이스나 이메일 같이 통합적인 기능을 제공하면서 라이브러리처럼 사용될 수 있는 것들이 있다.</p>
  <p>&nbsp;&nbsp;&nbsp;컴포넌트는 소프트웨어 활동 단위를, 모듈은 구현 단위와 산출물을 중점으로 하고 있다.</p>
</details>

<details>
  <summary><b>자바란 무엇인가</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;자바는 객체지향 프로그래밍 언어로서 보안성이 뛰어나며 컴파일한 코드는 다른 운영체제에서 사용될 수 있도록 클래스로 제공된다. C++의 객체지향적인 장점을 살리면서 분산환경을 지원해 효율적이다. 이러한 동작의 배경에는 JVM이 있다.</p>
</details>

<details>
  <summary><b>자바의 구동원리</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;자바로 작성한 코드는 `.java`라는 확장자를 가지며 자바에 존재하는 전용컴파일러 `javac`를 통해 컴파일 한다. 자바코드를 컴퓨터가 이해할 수 있도록 프로그래밍 언어에서 기계어로 변경되면 `.class` 확장자를 가지는 파일이 생성되고, 이 파일이 JVM을 통해서 실행된다.</p>
</details>

<details>
  <summary><b>JVM의 특징</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;JVM은 Java Virtual Machine의 약자로 자바 가상머신을 뜻한다. 자바소스로부터 만들어진 바이너리 파일(.class)을 실행하기 위해 필요하다. 자바가 OS에 구애받지 않고 사용가능하게 만들어주는 이유이기도 하다. 또한 자동 메모리 관리 기법인 Garbage Collection을 수행한다.</p>
  <p>
  &#128073; JRE : 자바 실행환경. JVM으로 자바자프로그램을 동작시킬 때 필요한 파일들을 가지고 있다.<br/>
  &#128073; JDK : Java 개발을 하기위해 필요한 환경. JDK에는 JRE가 포함되어 있다.
  </p>
</details>

<details>
  <summary><b>객체지향과 절차지향의 차이점</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;절차지향 프로그래밍이란 물이 위에서 아래로 흐르듯이 순차적인 처리가 중요시되며 프로그램 전체가 유기적으로 연결되도록 만드는 프로그래밍 기법이다. 컴퓨터의 처리구조와 유사하여 실행속도가 빠르다는 장점이 있지만 유지보수가 어렵고 실행순서가 정해져 있어 코드의 순서가 바뀌면 결과 값이 달라질 수 있고 디버깅이 어렵다는 단점이 있다.</p>
  <p>&nbsp;&nbsp;&nbsp;객체지향은 실제 세계를 모델링하여 소프트웨어를 개발하는 방법이다. 컴퓨터 부품을 하나씩 구해서 조립하는 것과 같이 프로그래밍한다. 코드의 재활용성이 높고 디버깅이 쉬운 장점이 있으나 절차지향에 비해 처리속도가 느리고 설계에 시간이 많이 걸린다는 단점이 있다.</p>
</details>
<br/>

<details>
  <summary><b>객체지향 언어의 특징</b></summary>
  <br/>
  <p>캡슐화<br/>관련된 데이터와 알고리즘이 하나의 묶음으로 정리된 것으로써 데이터를 감추고 외부세계와는 메소드를 통해 상호작용한다.</p>
  <p>상속 <br/>이미 작성된 클래스를 이어받아 새로운 클래스를 생성하는 기법으로 기존 코드를 재활용해서 사용한다.</p>
  <p>다형성 <br/>하나의 이름으로 많은 방법에 대처하는 기법이다.</p>
</details>

<details>
  <summary><b>상속과 구현의 차이점과 특징 및 장단점</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;</p>
</details>

<details>
  <summary><b>오버라이딩과 오버로딩의 차이점과 특징</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;오버로딩은 '많은 것을 싣는다', 오버라이딩은 '재정의한다'는 사전적 의미를 가진 만큼 차이점도 이와 비슷하다고 볼 수 있다.</p>
  <p>오버로딩은 하나의 클래스 안에서 인스턴스 개수나 형식이 다른 동일한 이름의 메소드를 여러 개 정의하는 것이고 정적 바인딩이다.</p>
  <p>오버라이딩은 상속을 했을 경우에 적용할 수 있고, 기존의 내용의 틀만 가져와서 재정의 하는 것이고 동적 바인딩이다.</p>
</details>

<details>
  <summary><b>기본형 변수와 참조형 변수는 무엇이 있나?</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;자바가 제공하는 기본형 변수는 총 8가지로 char, byte, short, boolean, int, long, float, double이 있다.</p>
  <p>참조형은 "값이 저장된 곳의 메모리 주소"를 저장하는 공간으로 객체의 주소를 저장한다. 기본형 8가지를 제외하고 모두 참조형이다.</p>
</details>

<details>
  <summary><b>스택 오버플로우가 왜 일어나는가?</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;스택포인터가 스택의 경계를 넘어설 때 발생한다. 호출 스택은 제안된 양의 주소공간을 이루며 프로그램 시작 시 결정된다.</p>
</details>
<br/>

<details>
  <summary><b>메모리 누수가 무엇인가?</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;프로그래밍에서 메모리 누수현상은 프로그램이 필요하지 않은 메모리를 계속 점유하고 있는 현상이다.</p>
  <p>자바에서 메모리 누수는 더 이상 사용하지 않는 객체가 가비지컬렉션(GC)에 의해 회수되지 않고 누적되는 현상이다. old 영역에 누적된 객체로 인해 메이저 GC가 빈번히 발생하게 되고 프로그램의 응답속도가 늦어지다가 결국 Out of memory 오류로 프로그램이 종료된다.</p>
</details>

<details>
  <summary><b>메모리 누수를 막기 위한 방법</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;가장 좋은 방법은 참조값을 갖는 변수가 최소한의 유효범위 안에 있도록 하는 것이다. 예를 들어 Local 변수로 만들 경우 자동으로 GC의 대상이 되는 것이 있다.</p>
</details>

<details>
  <summary><b>Static에 대한 설명</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;Static 키워드를 사용한다는 것은 어떠한 값이 메모리에 한번 할당되어 프로그램이 끝날 때 까지 그 메모리에 값이 유지된다는 것을 의미한다. 쉽게 말해 특정한 값을 공유해야 하는 상황이라면 Static 사용 시 메모리의 이점을 얻을 수 있다.</p>
</details>

<details>
  <summary><b>싱글톤 패턴이 무엇인가? 디자인 패턴이란?</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>익명 클래스와 익명 객체란?</b></summary>
  <br/>
  <p></p>
</details>
<br/>

<details>
  <summary><b>Java 문자열 검색</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>자료구조 특징, 장단점</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>쓰레드는 무엇이고 언제 쓰이는가?</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>멀티쓰레드와 쓰레드의 차이점</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>제네릭은 무엇인가?</b></summary>
  <br/>
  <p></p>
</details>
<br/>

<details>
  <summary><b>람다식이란 무엇인가?</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>힙과 스택의 차이점</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>TCP와 UDP 차이점</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>HTTP 프로토콜이란?</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>SQL injection 막는 법</b></summary>
  <br/>
  <p></p>
</details>
<br/>

<details>
  <summary><b>데이터베이스 설계부터 구현까지</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>DB 쿼리 테스트</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>스프링은 무엇인가?</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>프레임워크의 특징</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>IOC, DI, AOP (스프링 특징)</b></summary>
  <br/>
  <p></p>
</details>
<br/>

<details>
  <summary><b>인터셉터란? 어디에 사용되는가?</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>IOC, DI, AOP (스프링 특징)</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>세션과 쿠키의 차이</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>부트스트랩이란?</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>Git을 쓰는 이유와 깃과 깃허브의 차이</b></summary>
  <br/>
  <p></p>
</details>
<br/>

<details>
  <summary><b>자바스크립트란? 스크립트 언어란?</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>JQurey란? 특징?</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>JQuery 셀렉터</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>노드란?</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>노드 특징</b></summary>
  <br/>
  <p></p>
</details>
<br/>

<details>
  <summary><b>동기식과 비동기식 차이</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>Ajax란?</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>콜백 함수란?</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>클로저란?</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>JSP는 무엇인가?</b></summary>
  <br/>
  <p></p>
</details>
<br/>

<details>
  <summary><b>MVC는 무엇인가? 1, 2의 차이는?</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>RDBMS와 NoSQL 차이</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>알고리즘 테스트</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>MyBatis의 장점</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>URI, URL, URN은 무엇인가?</b></summary>
  <br/>
  <p></p>
</details>
<br/>

<details>
  <summary><b><code>==</code>과 equals() 차이</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>패스워드 암호화 방법</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>대칭키와 비대칭키 차이</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>Web Server와 WAS 차이</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>REST API란?</b></summary>
  <br/>
  <p></p>
</details>
<br/>

<details>
  <summary><b>Express란?</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>Node JS</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>GC 종류</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>CDN이란?</b></summary>
  <br/>
  <p></p>
</details>

<details>
  <summary><b>HTTP 1.1, 2 방식 차이</b></summary>
  <br/>
  <p></p>
</details>
<br/>

---
