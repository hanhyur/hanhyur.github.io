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
    + <details>
        <summary><b> HTTP & HTTPS (SSL) </b></summary>
        <p><b>HTTP(HyperText Transfer Protocol)</b></p>
        <p>&nbsp;인터넷 상에서 클라이언트와 서버가 자원을 주고 받을 때 쓰는 통신 규약<br/>
        HTTP는 텍스트 교환이므로, 누군가 네트워크에서 신호를 가로채면 내용이 노출되는 보안 이슈가 존재한다.<br/>
        &nbsp;이런 보안 문제를 해결해주는 프로토콜이 'HTTPS'</p>
        <br/>
        <p><b>HTTPS(HyperText Transfer Protocol Secure)</b></p>
        <p>&nbsp;인터넷 상에서 정보를 암호화하는 SSL 프로토콜을 사용해 클라이언트와 서버가 자원을 주고 받을 때 쓰는 통신 규약<br/>
        HTTPS는 텍스트를 암호화한다. (공개키 암호화 방식으로)</p>
        <br/>
        <p><b>HTTPS 통신 흐름</b></p>
        <p>1. 애플리케이션 서버(A)를 만드는 기업은 HTTPS를 적용하기 위해 공개키와 개인키를 만든다.</p>
        <p>2. 신뢰할 수 있는 CA 기업을 선택하고, 그 기업에게 내 공개키 관리를 부탁하며 계약을 한다.</p>  
        <p>CA란? : Certificate Authority로, 공개키를 저장해주는 신뢰성이 검증된 민간기업</p>
        <p>3. 계약 완료된 CA 기업은 해당 기업의 이름, A서버 공개키, 공개키 암호화 방법을 담은 인증서를 만들고, 해당 인증서를 CA 기업의 개인키로 암호화해서 A서버에게 제공한다.</p>
        <p>4. A서버는 암호화된 인증서를 갖게 되었다. 이제 A서버는 서버의 공개키로 암호화된 HTTPS 요청이 아닌 요청이 오면, 이 암호화된 인증서를 클라이언트에게 건내준다.</p>
        <p>5. 클라이언트가 main.html 파일을 달라고 A서버에 요청했다고 가정하자. HTTPS 요청이 아니기 때문에 CA기업이 A서버의 정보를 CA 기업의 개인키로 암호화한 인증서를 받게 된다. 이 때 브라우저는 이미 CA 기업의 공개키를 알고 있다.</p>
        <p>6. 브라우저는 해독한 뒤 A서버의 공개키를 얻게 되었다. 이제 A서버와 통신할 대는 얻은 A서버의 공개키로 암호화해서 요청을 날리게 된다.</p>
      </details>
    + <details>
        <summary><b> HTTP 1.1, 2.0, 3.0 </b></summary>
        <p><b>HTTP / 1.1</b></p>
        <p>&nbsp;1.0이 발표될 당시에 다양한 표준화가 진행되고 있었고, 1.0 발표가 된지 몇달 지나지 않아 HTTP의 첫번째 표준버전인 1.1이 발표<p>
        <p>- Connection Keep-Alive (기존 연결에 대해서 handshake 생략가능)<br/>
        - 파이프라이닝 추가, 이전 요청에 대한 응답이 완전히 전송되기 전에 다음 전송을 가능하게 하여 레이턴시를 낮춤<br/>
        - 청크된 응답 지원(응답 조각)<br/>
        - 캐시 제어 메커니즘<br/>
        - 언어, 인코딩 타입 등을 포함한 컨텐츠 전송<br/>
        - 동일 IP 주소에 다른 도메인을 호스트하는 기능 가능 (HOST header)</p>
        <p>&nbsp;TCP 연결 기반 위에서 동작하는 프로토콜로 신뢰성 확보를 위해 연결을 맺고 끊는 데 있어서 Handshake가 이루어짐<br/>
        &nbsp;기존 HTTP는 비연결성 프로토콜이기 때문에 한 번의 연결로 한 번의 요청과 응답을 하고 응답이 끝나면 연결을 끊음<br/>
        연결을 맺고 끊을 때마다 Handshake를 하기 때문에 비연결성 프로토콜에선 overhead 발생<br/>
        &nbsp;이에 HTTP/1.1에서 Keep-alive 기능이 추가되어 한 번 맺어졌던 연결을 끊지 않고 지속적으로 유지하여 불필요한 핸드 셰이크를 줄여 성능을 개선함</p>
        <p><b>HTTP/1.1의 단점</b></p>
        <p>- HTTP HOLB</br>
        &nbsp;파이프라이닝으로 인해 이전 요청이 끝나지 않으면 뒤 요청이 처리되지 않는 HOL(Head Of Line) Blocking 문제가 발생<br/>
        &nbsp;이를 해결하기 위해 클라이언트(브라우저) 요청을 병렬로 처리하는 커넥션을 이용하는 방식으로 성능 개선<br/>
        - RTT(Round Trip Time)<br/>
        &nbsp;TCP 상에서 동작하기 때문에 HandShake가 반복적으로 발생, 불필요한 RTT 증가와 네트워크 지연으로 성능 저하<br/>
        - 무거운 Header 구조<br/>
        &nbsp;1.1 헤더에는 많은 메타 정보들이 저장되어져 있음. 사용자가 방문한 웹 페이지는 다수의 http 요청이 발생하는데 매 요청마다 중복된 헤더 값을 전송하게 됨<br/>
        &nbsp;해당 domain에 설정된 쿠키 정보도 헤더에 포함되어 전송되므로 전송 값보다 헤더가 더 큰 경우도 생김.</p>
        <p><b>SPDY</b></p>
        <p>&nbsp;구글에서 더 빠른 Web을 실현하기 위해 구현한 프로토콜.<br/>
        &nbsp;HTTP를 대체하는 것이 아닌 전송을 재정의하는 형태로 구현되었고, 이는 HTTP/2.0 초안의 참고 규격이 됨</p>
        <p><b>HTTP / 2.0</b></p>
        <p>완전 새로운 프로토콜이 아닌 SPDY의 개선사항을 적용해 성능향상에 초점을 맞춘 프로토콜</p>
        <p>- Multiplexed Streams<br/>
        &nbsp;한 커넥션으로 동시에 여러 메세지를 주고 받을 수 있으며, 응답은 순서와 무관하게 stream으로 주고 받음<br/>
        <img src="https://velog.velcdn.com/images%2Fziyoonee%2Fpost%2F3620142f-a4dd-49b4-83ec-bd06cc875f6b%2FComparison-of-HTTP-versions.png" width="80%" align="center"><br/>
		- Stream Prioritization<br/>
    	&nbsp;리소스 간 의존관계(우선순위)를 설정. 렌더링이 늦어지는 문제 해결<br/>
		- Server Push<br/>
        &nbsp;필요한 리소스를 서버가 알아서 미리 보내줌.<br/>
		<img src="https://velog.velcdn.com/images%2Fziyoonee%2Fpost%2F75fd419e-4d67-42b0-9dc8-3b8e5c276a99%2Fhttp2-server-push-2.png" width="80%" align="center"><br/>
		- Header Compression<br/>
        &nbsp;헤더 정보 압축을 위해 HPACK 방식을 이용<br/>
		중복 값은 인덱스만 전송, 나머지는 Huffman Encoding 기법으로 처리하여 전송</p>
		<p><b>HTTP/2.0의 단점</b></p>
		<p>&nbsp;여전히 TCP를 이용하기 때문에 HandShake의 RTT로 인한 HOLB 문제는 해결하지 못함.</p>
		<p><b>QUIC</b></p>
		<p>&nbsp;Quick UDP Internet Connections 의 약자이며, UDP를 기반으로 TCP + TLS + HTTP 의 기능을 모두 구현하는 프로토콜로 HTTP/3의 기반 기술</p>
		<p><b>HTTP / 3.0</b></p>
		<p>UDP 기반의 프로토콜이지만 존의 신뢰성 있는 통신이라는 타이틀을 포기한 것은 아님</p>
		<p>- RTT 감소로 인한 지연시간 단축<br/>
		&nbsp;QUIC은 TCP를 사용하지 않기 때문에 통신을 시작할 때 번거로운 3 Way Handshake 과정을 거치지 않음<br/>
		TCP + TLS는 3 RTT가 필요한 반면, QUIC는  연결 설정에 필요한 정보와 함께 데이터를 전송하기 때문에 1 RTT만 소요됨.<br/>
		<img src="https://velog.velcdn.com/images%2Fziyoonee%2Fpost%2F54613d72-0211-4275-a5ec-cafec0d4f78f%2Fgcp-cloud-cdn-performance.gif" width="80%" align="center"><br/>
		단, 클라이언트가 서버로 첫 요청을 보낼 때는 서버의 세션 키를 모르는 상태이기 때문에 목적지인 서버의 Connection ID를 사용하여 생성한 특별한 키인 초기화 키(Initial Key)를 사용하여 통신을 암호화<br/>
		한번 연결에 성공했다면 서버는 그 설정을 캐싱해놓고 있다가, 다음 연결 때는 캐싱해놓은 설정을 사용하여 바로 연결을 성립시키기 때문에 0 RTT만으로 바로 통신을 시작할 수도 있음<br/>
		- 패킷 손실 감지에 걸리는 시간 단축<br/>
		&nbsp;TCP는 여러 ARQ 방식 중에서 Stop and Wait ARQ 방식을 사용, 대표적인 문제는 송신 측이 패킷을 수신측으로 보내고 난 후 얼마나 기다려줄 것인가, 즉 타임 아웃을 언제 낼 것인가를 동적으로 계산해야한다는 것<br/>
		타임스탬프를 사용할 수 있는 상황이라면 타임스탬프를 통해 패킷의 전송 순서를 파악할 수 있지만, 만약 사용할 수 없는 경우 시퀀스 번호에 기반하여 암묵적으로 전송 순서를 추론<br/>
		&nbsp;QUIC는 이런 불필요한 과정을 패킷마다 고유한 패킷 번호를 통해 해결함으로써 패킷 손실 감지에 걸리는 시간을 단축<br/>
		- 멀티플렉싱 지원<br/>
		- 클라이언트 IP가 변경되어도 연결이 유지됨<br/>
		&nbsp;TCP의 경우 소스의 IP 주소와 포트, 연결 대상의 IP 주소와 포트로 연결을 식별하기 때문에 클라이언트의 IP가 바뀌는 상황이 발생하면 연결이 끊어짐.<br/>
		연결이 끊어졌으니 다시 연결을 생성하기 위해 결국 Handshake 과정을 다시 거쳐야하고, 이 과정에서 다시 레이턴시가 발생<br/>
		&nbsp;QUIC은 Connection ID를 사용하여 서버와 연결을 생성. Connection ID는 랜덤한 값일 뿐, 클라이언트의 IP와는 전혀 무관한 데이터이기 때문에 클라이언트의 IP가 변경되더라도 기존의 연결을 계속 유지<br/>
		즉, 새로 연결을 생성할 때 거쳐야하는 핸드쉐이크 과정을 생략할 수 있음</p>
      </details>
    + <details>
        <summary><b> HTTP RESTful </b></summary>
        <p>HTTP(HyperText Transfer Protocol)</p>
        <p>인터넷 상에서 클라이언트와 서버가 자원을 주고 받을 때 쓰는 통신 규약<br/>
        HTTP는 텍스트 교환이므로, 누군가 네트워크에서 신호를 가로채면 내용이 노출되는 보안 이슈가 존재한다.<br/>
        이런 보안 문제를 해결해주는 프로토콜이 'HTTPS'</p>
        <p>HTTPS(HyperText Transfer Protocol Secure)</p>
        <p>인터넷 상에서 정보를 암호화하는 SSL 프로토콜을 사용해 클라이언트와 서버가 자원을 주고 받을 때 쓰는 통신 규약<br/>
        HTTPS는 텍스트를 암호화한다. (공개키 암호화 방식으로)</p>
      </details>
    + <details>
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