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
  <p>
  &#128073;캡슐화 : 관련된 데이터와 알고리즘이 하나의 묶음으로 정리된 것으로써 데이터를 감추고 외부세계와는 메소드를 통해 상호작용한다.
  </p>
  <p>
  &#128073;상속 : 이미 작성된 클래스를 이어받아 새로운 클래스를 생성하는 기법으로 기존 코드를 재활용해서 사용한다.
  </p>
  <p>
  &#128073;다형성 : 하나의 이름으로 많은 방법에 대처하는 기법이다.
  </p>
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
  <p>&nbsp;&nbsp;&nbsp;오버로딩은 하나의 클래스 안에서 인스턴스 개수나 형식이 다른 동일한 이름의 메소드를 여러 개 정의하는 것이고 정적 바인딩이다.</p>
  <p>&nbsp;&nbsp;&nbsp;오버라이딩은 상속을 했을 경우에 적용할 수 있고, 기존의 내용의 틀만 가져와서 재정의 하는 것이고 동적 바인딩이다.</p>
</details>

<details>
  <summary><b>기본형 변수와 참조형 변수는 무엇이 있나?</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;자바가 제공하는 기본형 변수는 총 8가지로 char, byte, short, boolean, int, long, float, double이 있다.</p>
  <p>&nbsp;&nbsp;&nbsp;참조형은 "값이 저장된 곳의 메모리 주소"를 저장하는 공간으로 객체의 주소를 저장한다. 기본형 8가지를 제외하고 모두 참조형이다.</p>
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
  <p>&nbsp;&nbsp;&nbsp;자바에서 메모리 누수는 더 이상 사용하지 않는 객체가 가비지컬렉션(GC)에 의해 회수되지 않고 누적되는 현상이다. old 영역에 누적된 객체로 인해 메이저 GC가 빈번히 발생하게 되고 프로그램의 응답속도가 늦어지다가 결국 Out of memory 오류로 프로그램이 종료된다.</p>
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
  <p>&nbsp;&nbsp;&nbsp;싱글톤 패턴은 애플리케이션이 시작될 때 어떤 클래스가 처음 한 번만 메모리를 할당하고(static) 그 메모리에 인스턴스를 만들어 사용하는 디자인패턴을 말한다.</p>
  <p>
  &#128073;사용하는 이유?<br/>
  1. 고정된 메모리 영역을 얻으면서 한번의 new로 인스턴스를 사용하기에 메모리 낭비 방지.<br/>
  2. 인스턴스가 한개만 존재하는걸 보증하고 싶을 때.<br/>
  3. 두번째 이용부터 객체로딩시간이 줄어 성능 향상.
  </p>
  <p>
  &#128073;문제점 : 싱글톤 인스턴스가 너무 많은 일을 하면 결합도가 높아져 '개방-폐쇄' 원칙 위반. 즉, 객체지향 설계의 원칙에 어긋남.
  </p>
  <p>&nbsp;&nbsp;&nbsp;디자인패턴이란 소프트웨어를 설계할 때 특정 맥락에서 자주 발생하는 고질적인 문제들이 또 발생했을 때 재사용할 수 있는 훌륭한 해결책.</p>
</details>

<details>
  <summary><b>익명 클래스와 익명 객체란?</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;익명클래스란 클래스의 이름이 없는 것을 말한다. 인터페이스를 구현하기 위해 해당 인터페이스를 구현할 클래스를 생성해야 하는데 일회성이고 재사용할 필요가 없다면 굳이 클래스를 만들 필요없이 익명클래스를 사용한다.</p>
</details>
<br/>

<details>
  <summary><b>Java 문자열 검색</b></summary>
  <br/>
  <p>
  &#128073;indexOf("문자열") : 특정 문자나 문자열이 앞에서부터 처음 발견되는 인덱스를 반환. 찾지 못했을 경우 "-1"을 반환.<br/>
  &#128073;contains("문자열") : 대상 문자열에 특정 문자열이 포함되어 있는지 확인. 대/소문자를 구분.<br/>
  &#128073;matches(정규식포함문자열) : 주어진 정규 표현식과 일치하는지 여부를 확인하는 함수. 정규 표현식을 사용하지 않아도 가능하지만 "정확히" 일치해야한다.
  </p>
</details>

<details>
  <summary><b>자료구조 특징, 장단점</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;</p>
</details>

<details>
  <summary><b>쓰레드는 무엇이고 언제 쓰이는가?</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;쓰레드는 하나의 프로세스 내부에서 독립적으로 실행되는 하나의 작업단위를 말한다. 세부적으로는 운영체제에 의해 관리되는 하나의 작업(task)를 의미한다.</p>
  <p>&nbsp;&nbsp;&nbsp;VM에 의해 하나의 프로세스가 발생되고 main()안의 실행문들이 하나의 스레드이다. main() 이외의 스레드를 만들려면 Thread클래스를 상속하거나 Runnable 인터페이스를 구현해야한다.</p>
</details>

<details>
  <summary><b>멀티쓰레드와 쓰레드의 차이점</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;여러 스레드를 동시에 실행시키는 응용프로그램을 작성하는 기법을 멀티쓰레드라고 한다. 메모리 공유로 시스템 자원 소모가 줄어들고 동시에 두가지 이상 작업이 가능하지만, 서로 자원을 소모하다 충돌할 가능성이 있고 코딩이 복잡해져 버그가 생길 가능성이 있다.</p>
</details>

<details>
  <summary><b>제네릭은 무엇인가?</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;제네릭은 다양한 타입의 객체들을 다루는 메소드나 컬렉션 클래스에 컴파일시의 타입체크를 해주는 기능이다. 즉 클래스 내부에서 사용할 데이터타입을 나중에 인스턴스를 생성 할 때 확정하는 것을 제네릭이라고 한다.</p>
</details>
<br/>

<details>
  <summary><b>람다식이란 무엇인가?</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;식별자 없이 실행이 가능한 함수. 함수를 따로 만들지 않고 코드 한 줄에 함수를 써서 그것을 호출하는 방식입니다. 자바 8부터 지원하고 코드가 간결해 가독성이 좋다는 것이 장점이다. 그러나 람다식으로 만든 함수는 재사용이 불가하고 디버깅이 까다롭다는 단점이 있다.</p>
  <p>P.S. 자바 8부터 지원하는 것 : 람다식, Optional, Stream</p>
</details>

<details>
  <summary><b>힙과 스택의 차이점</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;힙은 동적메모리를 사용하여 런타임 시 메모리영역을 원하는 크기로 잡을 수 있다. 스택은 정적메모리를 사용하여 컴파일 시 크기가 정해져 있다.</p>
</details>

<details>
  <summary><b>TCP와 UDP 차이점</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;TCP는 연결형 서비스를 지원하는 전송계층 프로토콜이며 인터넷 환경에서 주로 사용된다. 호스트 간 신뢰성있는 데이터 전달과 흐름 제어 및 혼잡 제어 등을 제공한다.<br/>
  예를 들어 전이중, 점대점 서비스가 있다.</p>
  <p>&nbsp;&nbsp;&nbsp;UDP는 비연결형 서비스를 지원하는 전송계층 프로토콜이며 인터넷 상에서 서로 정보를 주고 받을 때, 보내는 쪽에서 일방적으로 데이터를 전달하는 통신 프로토콜이다. 신뢰성이 낮지만 TCP의 핸드쉐이킹 같은 설정이 필요하지 않고, TCP보다 안정성은 떨어지지만 속도는 빠르다.</p>
</details>

<details>
  <summary><b>HTTP 프로토콜이란?</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;HTTP는 인터넷 상에서 데이터를 주고 받기 위한 서버/클라이언트 모델을 따르는 애플리케이션 레벨의 프로토콜로 TCP/IP 위에서 작동한다. HTTP는 어떤 종류의 데이터도 전송할 수 있도록 설계되어 있다.</p>
</details>

<details>
  <summary><b>SQL injection 막는 법</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;입력값에 대한 유효성 검사를 해주는 방법과 저장 프로시저를 사용하는 방법이 있다. 저장 프로시저는 사용하고자 하는 쿼리에 미리 형식을 지정해 보안성을 높일 수 있다.</p>
</details>
<br/>

<details>
  <summary><b>데이터베이스 설계부터 구현까지</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;전체 프로세스는 요구분석, 개념적 설계, 논리적 설계, 물리적 설계, 구현 순이다.<br/>
  1. 사용자의 DB 사용목적을 파악하고 요구조건 명세서를 작성한다.<br/>
  2. 트랜젝션 모델링과 개념 스키마 모델링을 수행하여 ER그램을 그리고 개념스키마를 설계한다.<br/>
  3. 논리적 설계에서는 DBMS에 논리적 스키마를 설계하고 트랜젝션 인터페이스를 설계한다.<br/>
  4. 그리고 설계된 DB를 실제 시스템 상에 구현하는 순서이다.</p>
</details>

<details>
  <summary><b>DB 쿼리 테스트</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;</p>
</details>

<details>
  <summary><b>스프링은 무엇인가?</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;스프링은 자바 플랫폼을 위한 오픈소스 애플리케이션 프레임워크로서 동적인 웹사이트를 개발하기 위해 여러가지 서비스를 제공해준다. 자바 개발을 위한 프레임워크로 종속 객체를 생성해주고 조립해준다.</p>
</details>

<details>
  <summary><b>프레임워크의 특징</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;프레임워크는 구체적이며 확장 가능한 기반코드를 가지고 있으며, 설계자가 의도하는 여러 디자인 패턴의 집합으로 구성되어 있다. 장점으로는 구조화, 추상화, 재사용성이 있고 좀 더 구조적이고 안정적이면서 유지보수가 쉽고 확장성이 뛰어나게 하기위해 생겼다.</p>
</details>

<details>
  <summary><b>IoC, DI, AOP (스프링 특징)</b></summary>
  <br/>
  <p>
  &#128073;IoC(Inversion of Control, 제어의 역행) : 메소드나 객체의 호출작업을 개발자가 결정하는 것이 아니라 외부에서 결정되는 것을 의미한다. 개발자는 필요한 부분을 개발해서 '끼워넣기'의 형태로 개발하고 실행된다. 최종 호출이 개발자가 아닌 프레임워크 내부에서 결정되는대로 이뤄지게 되는 것을 의미한다.
  </p>
  <p>
  &#128073;DI(Dependency Injection, 의존성 주입) : IoC가 일어날 때 스프링이 내부에 있는 객체들 간의 관계를 관리할 때 사용하는 기법. 의존성 주입은 말 그대로 의존적인 객체를 직접 생성하거나 제어하는 것이 아니라, 제어의 역행으로 특정 객체에 필요한 객체를 외부에서 결정해서 연결시키는 것을 의미한다.
  </p>
  <p>
  &#128073;AOP(Aspect-Oriented Programming, 관점지향 프로그래밍) : 기능을 핵심 비즈니스 로직과 공통모듈로 구분하고, 핵심 로직에 영향을 미치지 않으면서 사이사이에 공통 모듈을 효과적으로 잘 끼워넣도록 하는 프로그래밍 패러다임. 공통모듈(보안 인증, 로깅 같은 요소 등)을 만든 후에 코드 밖에서 이 모듈을 비즈니스 로직에 삽입하는 것이 바로 AOP적인 개발이다. 코드 밖에서 설정된다는 것이 핵심이다.
  </p>
</details>
<br/>

<details>
  <summary><b>인터셉터란? 어디에 사용되는가?</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;</p>
</details>

<details>
  <summary><b>스프링 시큐어리티란?</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;스프링 기반의 애플리케이션의 보안(인증과 권한, 인가)을 담당하는 스프링 하위 프레임워크이다. 주로 서블릿 필터와 이들로 구성된 필터 체인으로의 위임모델을 사용한다. 그리고 보안과 관련해서 체계적으로 많은 옵션을 제공해주기 때문에 개발자 입장에서는 일일이 보안관련 로직을 생성하지 않아도 된다.</p>
</details>

<details>
  <summary><b>쿠키와 세션의 차이</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;쿠키는 클라이언트의 로컬에 저장되는 키와 값의 작은 데이터이다. 텍스트 형식으로 저장되며, 브라우저 종료 시에도 로컬에 남아있고 도메인당 20개, 쿠키당 4kb의 제한이 있다.</p>
  <p>&nbsp;&nbsp;&nbsp;세션은 일정시간동안 같은 브라우저로 들어오는 일련의 요구사항을 하나의 상태로 보고 그 상태를 유지하는 기능이다. 서버에 오브젝트 형식으로 저장되며 브라우저 종료 시 사라진다.</p>
</details>

<details>
  <summary><b>부트스트랩이란?</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;프론트엔드 개발을 빠르고 쉽게 할 수 있는 프레임워크. 반응형이며 HTML, CSS, JS에 템플릿 요소들을 미리 정의해놓은 것이다. 깃 허브 오픈소스로 활용 가능하며, 상업적으로도 이용가능하다.</p>
</details>

<details>
  <summary><b>Git을 쓰는 이유와 깃과 깃허브의 차이점</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;git은 형상관리 도구 중 하나로 버전관리 시스템이라고도 한다. Git은 소스코드를 효과적으로 관리할 수 있게 해주는 무료 공개 소프트웨어이다.</p>
  <p>&nbsp;&nbsp;&nbsp;git과 SVN의 차이점은 SVN은 중앙서버에 소스코드와 히스토리를 저장하는 반면, git은 여러 PC와 저장소에 분산해서 저장한다. 그래서 중앙서버에 장애가 발생해도 커밋과 복원이 가능하다. 사본을 로컬에서 관리하기 때문에 git이 SVN보다 빠르다.</p>
  <p>&nbsp;&nbsp;&nbsp;gitHub는 local에서 관리한 소스코드를 업로드하고 공유할 수 있는 공간이다.</p>
</details>
<br/>

<details>
  <summary><b>자바스크립트란? 스크립트 언어란?</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;스크립트 언어는 소스코드를 컴파일 하지 않고도 실행할 수 있는 프로그래밍 언어를 말한다. C와 자바는 개발자가 작성한 코드를 컴파일러를 통해 기계어로 변경해야 하지만 스크립트 언어는 컴파일 과정 없이 내장된 번역기에 의해 실행된다.</p>
  <p>&nbsp;&nbsp;&nbsp;자바스크립트는 쉽게 말해 웹을 풍부하게 만들어주는 작고 가벼운 언어이다. 웹 브라우저에서 실행하는 스크립트 언어를 기술하며 작고 빠르기 때문에 동적인 웹 문서를 꾸밀 때 주로 사용된다. 객체 기반의 언어이지만, 상속과 클래스 개념은 없고 HTML에다가 연산제어 등 프로그래밍적 요소가 추가된 것이 장점이다.</p>
</details>

<details>
  <summary><b>JQurey란? 특징?</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;</p>
</details>

<details>
  <summary><b>JQuery 셀렉터</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;</p>
</details>

<details>
  <summary><b>노드란?</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;</p>
</details>

<details>
  <summary><b>노드 특징</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;</p>
</details>
<br/>

<details>
  <summary><b>동기식과 비동기식 차이</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;</p>
</details>

<details>
  <summary><b>Ajax란?</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;</p>
</details>

<details>
  <summary><b>콜백 함수란?</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;</p>
</details>

<details>
  <summary><b>클로저란?</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;</p>
</details>

<details>
  <summary><b>JSP는 무엇인가?</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;</p>
</details>
<br/>

<details>
  <summary><b>MVC는 무엇인가? 1, 2의 차이는?</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;</p>
</details>

<details>
  <summary><b>RDBMS와 NoSQL 차이</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;</p>
</details>

<details>
  <summary><b>알고리즘 테스트</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;</p>
</details>

<details>
  <summary><b>MyBatis의 장점</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;</p>
</details>

<details>
  <summary><b>URI, URL, URN은 무엇인가?</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;</p>
</details>
<br/>

<details>
  <summary><b><code>==</code>과 equals() 차이</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;</p>
</details>

<details>
  <summary><b>패스워드 암호화 방법</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;</p>
</details>

<details>
  <summary><b>대칭키와 비대칭키 차이</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;</p>
</details>

<details>
  <summary><b>Web Server와 WAS 차이</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;</p>
</details>

<details>
  <summary><b>REST API란?</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;</p>
</details>
<br/>

<details>
  <summary><b>Express란?</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;</p>
</details>

<details>
  <summary><b>Node JS</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;</p>
</details>

<details>
  <summary><b>GC 종류</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;</p>
</details>

<details>
  <summary><b>CDN이란?</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;</p>
</details>

<details>
  <summary><b>HTTP 1.1, 2 방식 차이</b></summary>
  <br/>
  <p>&nbsp;&nbsp;&nbsp;</p>
</details>
<br/>

---
