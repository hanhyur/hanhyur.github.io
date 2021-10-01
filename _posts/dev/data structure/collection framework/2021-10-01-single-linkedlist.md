---
layout: post
date: 2021-10-01 23:24:00
title: "Single LinkeList"
description: "자료구조"
subject: dev
category: [cs]
tags: [cs, data structure, collection framework]
use_math: true
comments: true
---

# Singly LinkedList

&nbsp;&nbsp;&nbsp;이번에는 LinkedList의 한 종류인 Singly LinkedList를 구현해보자. 

LinkedList의 경우 ArrayList와 가장 큰 차이점이 바로 '노드'라는 객체를 이용하여 연결한다는 것이다. ArrayList의 경우 최상위 타입인 오브젝트 배열(Object[])을 사용하여 데이터를 담아두었다면, LinkedList는 배열을 이용하는 것이 아닌 하나의 객체를 두고 그 안에 데이터와 다른 노드를 가리키는 레퍼런스 데이터로 구성하여 여러 노드를 하나의 체인(chain)처럼 연결하는 것이다.

&nbsp;&nbsp;&nbsp;앞서 데이터와 다른 노드를 가리킬 주소 데이터를 담을 객체, 노드(node)가 필요하다고 했다. ~~우리는 이것을 '노드(node)'라고 부르기로 약속했어요~~. node가 가장 기초적 단위라고 보면 된다.

노드 하나의 구조는 다음과 같다.

<img src="https://img1.daumcdn.net/thumb/R720x0.q80/?scode=mtistory2&fname=http%3A%2F%2Fcfile26.uf.tistory.com%2Fimage%2F23490844589151461C973D
" width="70%" height="auto" align="center"><br/>

---
**Reference**

- [공식 문서](https://docs.oracle.com/javase/8/docs/api/)
- <https://st-lab.tistory.com/167>

---
<a name="footnote_1">1</a> :