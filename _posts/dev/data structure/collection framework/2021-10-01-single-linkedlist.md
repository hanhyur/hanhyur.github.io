---
layout: post
date: 2021-10-01 23:24:00
title: "Single LinkedList"
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

위 구조에서 사용자가 저장할 데이터는 data 변수에 담기고, 다음에 연결할 노드를 가리키는 데이터가 담기는 부분을 reference 데이터(참조 데이터)라고 한다. 이러한 노드 여러 개가 연결되어 있는 것을 연결 리스트, 즉 LinkedList라고 한다.

각각의 래퍼런스 변수는 다음 노드객체를 가리키는데 단방향으로 연결 된 리스트를 LinkedList 중에서도 `Singly LinkedList`라고 한다.

## Node 구현

&nbsp;&nbsp;&nbsp;LinkedList를 구현하기에 앞서 가장 기본적인 데이터를 담을 Node 클래스를 먼저 구현하자.

```java
class Node<E> {

	E data;
	Node<E> next;	// 래퍼런스 변수

	Node(E data) {
		this.data = data;
		this.next = null;
	}

}
```

next는 Reference 변수로 다음 노드를 가리키는 변수이므로 '노드 자체'를 가리키기 때문에 타입을 `Node<E>` 타입으로 지정해주어야 한다.

# Singly LinkedList 구현

### 필수 목록

- 클래스 및 생성자 구성
- search 메서드 구현
- add 메서드 구현
- get, set, indexOf, contains 메서드 구현
- remove 메서드 구현
- size, isEmpty, clear 메서드 구현

### 부가 목록

- clone, toArray, sort 메서드 구현

## Singly LinkedList 클래스 및 생성자 구성하기

&nbsp;&nbsp;&nbsp;사전에 만들어두었던 List 인터페이스를 implements 해준다. implements를 하면 class 옆에 경고표시가 뜨는데 이는 앞서 경험한 것과 같이 List 인터페이스에 있는 메서드들을 구현하라는 의미이므로 무시한다.

```java
import Interface_form.List;

public class SinglyLinkedList<E> implements List<E> {

	private Node<E> head;
	private Node<E> tail;
	private int size;

	public SinglyLinkedList() {
		this.head = null;
		this.tail = null;
		this.size = 0;
	}

}
```

처음 단일 연결리스트를 생성 할 때에는 아무런 데이터가 없으므로 당연히 head와 tail이 가리킬 노드가 없기에 null로, size는 0으로 초기화하자.

메인 클래스에서 객체생성 한다면 아래와 같을 것이다.

```java
SinglyLinkedList<Integer> list = new SinglyLinkedList<>();
```

## search 메서드 구현

&nbsp;&nbsp;&nbsp;본격적인 구현에 앞서 search() 메서드를 먼저 만들자. 단일 연결리스트이다보니 특정 위치의 데이터를 삽입, 삭제, 검색하기 위해서 처음 노드(head)부터 next변수를 통해 특정 위치까지 찾아가야 하기 때문이다. 미리 search() 메서드를 구현해놓고 쓰면 매우 편리하니 미리 구현해놓자.

```java
// 특정 위치의 노드를 반환
private Node<E> search(int index) {

  // 범위 밖(잘못된 위치)일 경우 예외 던지기 
	if(index < 0 || index >= size) {
		throw new IndexOutOfBoundsException();
	}

	Node<E> x = head; // head가 기리키는 노드부터 시작 

	for (int i = 0; i < index; i++) {
		x = x.next; // x노드의 다음 노드를 x에 저장
	}

	return x;

}
```

---
**Reference**

- [공식 문서](https://docs.oracle.com/javase/8/docs/api/)
- <https://st-lab.tistory.com/167>

---
<a name="footnote_1">1</a> :