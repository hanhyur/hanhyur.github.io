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

1. HTTP 관련 질문 - HTTP, HTTPS(SSL) / HTTP 1.1, 2.0, 3.0 / HTTP RESTful

<details>
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