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


---
<a name="footnote_1">1</a> : <https://www.ietf.org/rfc/rfc3986.txt> - 1.1.3. URI, URL, and URN