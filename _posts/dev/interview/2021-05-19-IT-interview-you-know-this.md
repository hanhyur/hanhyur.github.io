---
layout: post
date: 2021-05-19 18:45:00
title: "이건 꼭 알고 가자! 면접 출제 빈도가 높은 질문들"
description: "IT 기술 면접"
subject: Interview
category: [ cs ]
tags: [ cs, interview, it ]
comments: true
---

다음 질문들은 IT 기술 면접에서 자주 나오는 공통된 질문들이다.
질문에 대한 답을 추가해 놓을 예정이지만, <b>직접 검색해서 찾아보는 것을 추천한다.</b>

---

1. HTTP 관련 질문 - HTTP, HTTPS(SSL) / HTTP 1.1, 2.0, 3.0 / HTTP RESTful / HTTP 응답코드
    - <details>
        <summary><b> HTTP & HTTPS (SSL) </b></summary>
        <p><b>HTTP(HyperText Transfer Protocol)</b></p>
        <p>인터넷 상에서 클라이언트와 서버가 자원을 주고 받을 때 쓰는 통신 규약<br/>
        HTTP는 텍스트 교환이므로, 누군가 네트워크에서 신호를 가로채면 내용이 노출되는 보안 이슈가 존재한다.<br/>
        이런 보안 문제를 해결해주는 프로토콜이 'HTTPS'</p>
        <br/>
        <p><b>HTTPS(HyperText Transfer Protocol Secure)</b></p>
        <p>인터넷 상에서 정보를 암호화하는 SSL 프로토콜을 사용해 클라이언트와 서버가 자원을 주고 받을 때 쓰는 통신 규약<br/>
        HTTPS는 텍스트를 암호화한다. (공개키 암호화 방식으로)</p>
        <br/>
        <p><b>HTTPS 통신 흐름</b></p>
        <p>1. 애플리케이션 서버(A)를 만드는 기업은 HTTPS를 적용하기 위해 공개키와 개인키를 만든다.<br/>
        2. 신뢰할 수 있는 CA 기업을 선택하고, 그 기업에게 내 공개키 관리를 부탁하며 계약을 한다.<br/>
        CA란? : Certificate Authority로, 공개키를 저장해주는 신뢰성이 검증된 민간기업<br/>
        3. 계약 완료된 CA 기업은 해당 기업의 이름, A서버 공개키, 공개키 암호화 방법을 담은 인증서를 만들고,<br/>
        해당 인증서를 CA 기업의 개인키로 암호화해서 A서버에게 제공한다.<br/>
        4. A서버는 암호화된 인증서를 갖게 되었다. 이제 A서버는 A서버의 공개키로 암호화된 HTTPS 요청이 아닌 요청이 오면,<br/>
        이 암호화된 인증서를 클라이언트에게 건내준다.<br/>
        5. 클라이언트가 main.html 파일을 달라고 A서버에 요청했다고 가정하자. HTTPS 요청이 아니기 때문에 CA기업이 <br/>
        A서버의 정보를 CA 기업의 개인키로 암호화한 인증서를 받게 된다. 이 때 브라우저는 이미 CA 기업의 공개키를 알고 있다.<br/>
        6. 브라우저는 해독한 뒤 A서버의 공개키를 얻게 되었다. 이제 A서버와 통신할 대는 얻은 A서버의 공개키로 암호화해서 요청을 날리게 된다.</p>
    </details>
    - <details>
        <summary><b> HTTP 1.1, 2.0, 3.0 </b></summary>
        <p><b>HTTP / 1.1</b></p>
        <p>1.0이 발표될 당시에 다양한 표준화가 진행되고 있었고, 1.0 발표가 된지 몇달 지나지 않아 HTTP의 첫번째 표준버전인 1.1이 발표<br/>
        + Connection Keep-Alive (기존 연결에 대해서 handshake 생략가능)<br/>
        + 파이프라이닝 추가, 이전 요청에 대한 응답이 완전히 전송되기 전에 다음 전송을 가능하게 하여 레이턴시를 낮춤<br/>
        + 청크된 응답 지원(응답 조각)<br/>
        + 캐시 제어 메커니즘<br/>
        + 언어, 인코딩 타입 등을 포함한 컨텐츠 전송<br/>
        + 동일 IP 주소에 다른 도메인을 호스트하는 기능 가능 (HOST header)<br/>
        TCP 연결 기반 위에서 동작하는 프로토콜로 신뢰성 확보를 위해 연결을 맺고 끊는 데 있어서 Handshake가 이루어짐<br/>
        기존 HTTP는 비연결성 프로토콜이기 때문에 한 번의 연결로 한 번의 요청과 응답을 하고 응답이 끝나면 연결을 끊음<br/>
        연결을 맺고 끊을 때마다 Handshake를 하기 때문에 비연결성 프로토콜에선 overhead 발생<br/>
        이에 HTTP/1.1에서 Keep-alive 기능이 추가되어 한 번 맺어졌던 연결을 끊지 않고 지속적으로 유지하여 불필요한 핸드 셰이크를 줄여 성능을 개선함</p>
        <p><b>HTTP/1.1의 단점</b></p>
        <p>+ HTTP HOLB</br>
        파이프라이닝으로 인해 이전 요청이 끝나지 않으면 뒤 요청이 처리되지 않는 HOL(Head Of Line) Blocking 문제가 발생<br/>
        이를 해결하기 위해 클라이언트(브라우저) 요청을 병렬로 처리하는 커넥션을 이용하는 방식으로 성능 개선<br/>
        + RTT(Round Trip Time)<br/>
        TCP 상에서 동작하기 때문에 HandShake가 반복적으로 발생<br/>
        불필요한 RTT 증가와 네트워크 지연으로 성능 저하<br/>
        </p>
    </details>
    - <details>
        <summary><b> HTTP RESTful </b></summary>
        <p>HTTP(HyperText Transfer Protocol)</p>
        <p>인터넷 상에서 클라이언트와 서버가 자원을 주고 받을 때 쓰는 통신 규약<br/>
        HTTP는 텍스트 교환이므로, 누군가 네트워크에서 신호를 가로채면 내용이 노출되는 보안 이슈가 존재한다.<br/>
        이런 보안 문제를 해결해주는 프로토콜이 'HTTPS'</p>
        <p>HTTPS(HyperText Transfer Protocol Secure)</p>
        <p>인터넷 상에서 정보를 암호화하는 SSL 프로토콜을 사용해 클라이언트와 서버가 자원을 주고 받을 때 쓰는 통신 규약<br/>
        HTTPS는 텍스트를 암호화한다. (공개키 암호화 방식으로)</p>
    </details>
    - <details>
        <summary><b> HTTP 응답코드 </b></summary>
        <p>HTTP(HyperText Transfer Protocol)</p>
        <p>인터넷 상에서 클라이언트와 서버가 자원을 주고 받을 때 쓰는 통신 규약<br/>
        HTTP는 텍스트 교환이므로, 누군가 네트워크에서 신호를 가로채면 내용이 노출되는 보안 이슈가 존재한다.<br/>
        이런 보안 문제를 해결해주는 프로토콜이 'HTTPS'</p>
        <p>HTTPS(HyperText Transfer Protocol Secure)</p>
        <p>인터넷 상에서 정보를 암호화하는 SSL 프로토콜을 사용해 클라이언트와 서버가 자원을 주고 받을 때 쓰는 통신 규약<br/>
        HTTPS는 텍스트를 암호화한다. (공개키 암호화 방식으로)</p>
    </details>

2. 웹 브라우저에 google.com을 입력하면 일어나는 과정

3. OS 쓰레드, 프로세스 차이 (멀티쓰레드와 멀티프로세스 차이, PCB)

4. DB 트랜잭션과 트랜잭션 특징 4가지

5. OS 데드락, 데드락의 조건 4가지, 동기화(Mutex, Semaphore, Monitor, Spinlock, Atomic 설명)

6. 언어관련 지식
    - 예를 들어 Java의 경우
        - JVM, GC / JAVA 객체지향, 솔리드, 프로젝트 실행의 과정
    - 객체지향 vs 절차지향, 오버라이딩 & 오버로딩, 인터페이스, 추상클래스, 가상함수 등등

7. TCP vs UDP (각각의 특성)

8. Segmentation과 내부 단편화, Paging과 외부 단편화

9. DB 인덱스, 인덱스를 거는 이유, 인덱스에 해쉬를 왜 사용할 수 없는가

10. 메모리 구조 / 스택 / 힙 / 데이터 / 코드 영역
    - 선언을 했을 때 어느 쪽에 저장이 되는지 설명

11. Map vs HashMap / List vs Array / Stack vs Queue

12. 정렬의 종류, 퀵소트 설명 (손으로 코딩해봐라)

13. OSI 계층 설명 (각각 알려진 유명 프로토콜)

14. 직무 관련, 포트폴리오 관련 질문

---
**Reference**  
+ [갓등님 블로그](https://garden1500.tistory.com/11)