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

## add 메서드 구현

&nbsp;&nbsp;&nbsp;SinglyLinkedList에 데이터를 추가할 수 있도록 리스트 인터페이스에 있는 add() 메서드를 구현해야한다. add() 메서드를 Override(재정의)를 한다고 보면 된다.  
add 메서드에는 ArrayList와 마찬가지로 크게 3가지로 분류가 된다.

- 가장 앞부분에 추가 - addFirst(E value)
- 가장 마지막 부분에 추가 (기본값) - addLast(E value)
- 특정 위치에 추가 - add(int index, E value) 

자바에 내장되어있는 LinkedList에서는 add() 역할을 addLast() 메서드가 하고, 특정 위치에 추가는 add(int index, E element) 메서드, 가장 첫 부분에 추가는 addFisrt()가 한다.

### 1. addFisrt(E value)

&nbsp;&nbsp;&nbsp;기본 값인 add()및 addLast()를 구현하기 전에 먼저 addFisrt()를 구현해보자. 그 이유는 addLast()를 구현 할 때 addFirst()를 사용하기 때문이다. 이 부분은 addLast()를 구현 할 때 다시 한 번 확인하자.

데이터를 이동시키는 것이 아닌 새로운 노드를 생성하고 새 노드의 레퍼런스 변수(next)가 head 노드를 가리키도록 구현해주면 된다.

```java
public void addFirst(E value) {
 
	Node<E> newNode = new Node<E>(value);	// 새 노드 생성
	newNode.next = head;	// 새 노드의 다음 노드로 head 노드를 연결
	head = newNode;	// head가 가리키는 노드를 새 노드로 변경
	size++;
 
	/**
	 * 다음에 가리킬 노드가 없는 경우(=데이터가 새 노드밖에 없는 경우)
	 * 데이터가 한 개(새 노드)밖에 없으므로 새 노드는 처음 시작노드이자
	 * 마지막 노드다. 즉 tail = head 다.
	 */
	if (head.next == null) {
		tail = head;
	}

}
```

새 노드(newNode)를 하나 만들어 준 다음 '가장 앞에 추가'해야 하므로 기존에 있던 head가 가리키는 노드 앞에 존재해야 한다. 즉, 새로운 노드의 next가 다음 노드인 head가 되는 것이다. 그렇게 링크를 연결해주었다면, head가 가리키는 첫 번째 노드를 새로운 노드로 변경해주고 사이즈를 1 증가시키면 된다.

예외적으로 아무런 노드가 없는 상태에서 처음으로 추가하는 노드인 경우 결국 head가 가리키는 노드는 없다. 이는 head 노드(새로운 노드)가 처음이자 마지막 노드가 된다는 말이기 때문에 마지막을 가리키는 변수 tail은 곧 head와 같은 노드를 가리키게 된다.

### 2. 기본삽입 : add(E value) & addLast(E value) 메서드

&nbsp;&nbsp;&nbsp;add()의 기본 값은 addLast()이다. LinkedLIst API를 보면 add메서드를 호출하면 add() 메서드는 addLast()를 호출한다. 즉, 구현 자체는 addLast를 중점적으로 구현하면 된다.  
addFirst()를 먼저 구현한 이유는 addLast()를 사용했는데 size가 0일 경우(=아무런 노드가 없는 경우) 처음으로 데이터를 추가하는 것이기 때문에 addFirst()를 호출해주면 되므로 addFirst()를 먼저 구현했다.

데이터를 이동시키는 것이 아닌 새로운 노드를 생성하고 이전 노드의 레퍼런스 변수(next)가 새로운 노드를 가리키도록 해주면 된다.

```java
@Override
public boolean add(E value) {
	addLast(value);
	return true;
}

public void addLast(E value) {
	Node<E> newNode = new Node<E>(value);	// 새 노드 생성 

	if (size == 0) {	// 처음 넣는 노드일 경우 addFisrt로 추가
		addFirst(value);
		return;
	}

	/**
	 * 마지막 노드(tail)의 다음 노드(next)가 새 노드를 가리키도록 하고
	 * tail이 가리키는 노드를 새 노드로 바꿔준다. 
	 */
	tail.next = newNode;
	tail = newNode;
	size++;
}
```

기존에 있던 tail 노드가 다음 노드를 가리키는 변수(next)를 새 노드를 가리키도록 변경하고 tail이 가리키는 노드를 새로운 노드로 변경만 해주면 된다.

### 3. add(int index, E value)

&nbsp;&nbsp;&nbsp;구현할 때 처리해야 할 부분이 많은데 넣으려는 위치의 앞 뒤로 링크를 새로 업데이트 해주어야 하기 때문이다.

먼저 넣으려는 위치(예를들어 index = 3)의 노드와 이전의 노드를 찾아야 한다. 넣으려는 위치의 이전노드를 prev_Node라고 하고, 넣으려는 위치의 기존노드를 next_Node라고 할 때, 앞서 우리가 만든 메서드 search()를 사용하여 넣으려는 위치 - 1의 노드(prev_Node)를 찾아내고, next_Node는 prev_Node.next를 통해 찾는다. 그리고 prev_Node의 링크를 새로 추가하려는 노드로 변경하고, 새로 추가하려는 노드의 링크는 next_Node로 변경해주는 것이다.  
다만 index 변수가 잘못된 위치를 참조할 수 있으니 이에 대한 예외처리로 IndexOutOfBoundsException을 한다.

```java
@Override
public void add(int index, E value) {

	// 잘못된 인덱스를 참조할 경우 예외 발생
	if (index > size || index < 0) {
		throw new IndexOutOfBoundsException();
	}

	// 추가하려는 index가 가장 앞에 추가하려는 경우 addFirst 호출 
	if (index == 0) {
		addFirst(value);
		return;
	}

	// 추가하려는 index가 마지막 위치일 경우 addLast 호출
	if (index == size) {
		addLast(value);
		return;
	}

	// 추가하려는 위치의 이전 노드 
	Node<E> prev_Node = search(index - 1);

	// 추가하려는 위치의 노드
	Node<E> next_Node = prev_Node.next;

	// 추가하려는 노드
	Node<E> newNode = new Node<E>(value);	

	/**
	 * 이전 노드가 가리키는 노드를 끊은 뒤
	 * 새 노드로 변경해준다. 
	 * 또한 새 노드가 가리키는 노드는 next_Node로
	 * 설정해준다. 
	 */
	prev_Node.next = null;
	prev_Node.next = newNode;
	newNode.next = next_Node;
	size++;

}
```

## remove 메서드 구현

&nbsp;&nbsp;&nbsp;추가해주었다면 반대로 삭제도 할 수 있어야 할테니 remove() 메서드를 구현해보자. 쉽게 생각해서 add() 메서드의 메커니즘을 반대로 생각하면 된다.

remove 메서드의 경우 크게 3가지로 나눌 수 있다.

- 가장 앞의 요소(head)를 삭제 - remove()
- 특정 index의 요소를 삭제 - remove(int index)
- 특정 요소를 삭제 - remove(Object value)

기본적으로 삭제 연산의 가장 기초는 remove() 메서드로 head가 가리키는 요소, 첫 번째 요소를 삭제하는 것이다. 인덱스로 생각한다면 0 위치에 있는 요소를 말한다. 그리고 다른 remove() 메서드들을 구현할 때 자칫 잘못해서 null을 참조하거나 잘못된 참조를 하는 경우도 있으니 신중하게 작성하자.

### 1. remove() 메서드

&nbsp;&nbsp;&nbsp;remove()는 '가장 앞에 있는 요소'를 제거하는 것이다. 즉, head가 가리키는 요소만 없애주면 된다.

head가 가리키는 노드의 링크와 데이터를 null로 지워준 뒤 head를 다음 노드로 업데이트를 해주는 방식이다. 그리고 삭제하려는 노드가 리스트에서의 유일한 노드였을 경우(요소가 한 개일 경우 head와 tail이 가리키는 노드가 동일) 해당 노드를 삭제하면 tail이 가리키는 노드 또한 없어지게 된다. 이에 대해서도 정확하게 처리를 해주자.

```java
public E remove() {

	Node<E> headNode = head;

	if (headNode == null) {
		throw new NoSuchElementException();
	}

	// 삭제된 노드를 반환하기 위한 임시 변수
	E element = headNode.data;

	// head의 다음 노드 
	Node<E> nextNode = head.next;

	// head 노드의 데이터들을 모두 삭제
	head.data = null;
	head.next = null;

	// head 가 다음 노드를 가리키도록 업데이트
	head = nextNode;
	size--;

	/**
	 * 삭제된 요소가 리스트의 유일한 요소였을 경우
	 * 그 요소는 head 이자 tail이었으므로 
	 * 삭제되면서 tail도 가리킬 요소가 없기 때문에
	 * size가 0일경우 tail도 null로 변환
	 */
	if(size == 0) {
		tail = null;
	}

	return element;

}
```

### 2. remove(int index) 메서드

&nbsp;&nbsp;&nbsp;remove(int index) 메서드는 사용자가 원하는 특정 위치(index)를 리스트에서 찾아서 삭제하는 것이다. add(int index, E value)의 반대이다.

삭제하려는 노드의 이전 노드의 next 변수를 삭제하려는 노드의 다음 노드를 가리키도록 해주면 된다. 그리고 index를 범위 밖으로 입력했을 경우의 예외 또한 던져주도록 하자.

```java
@Override
public E remove(int index) {

	// 삭제하려는 노드가 첫 번째 원소일 경우 
	if (index == 0) {
		return remove();
	}

	// 잘못된 범위에 대한 예외 
	if (index >= size || index < 0) {
		throw new IndexOutOfBoundsException();
	}

	Node<E> prevNode = search(index - 1);	// 삭제할 노드의 이전 노드 
	Node<E> removedNode = prevNode.next;	// 삭제할 노드 
	Node<E> nextNode = removedNode.next;	// 삭제할 노드의 다음 노드 

	E element = removedNode.data;	// 삭제되는 노드의 데이터를 반환하기 위한 임시변수

	// 이전 노드가 가리키는 노드를 삭제하려는 노드의 다음노드로 변경 
	prevNode.next = nextNode;

	// 데이터 삭제 
	removedNode.next = null;
	removedNode.data = null;
	size--;

	return element;

}
```

기존에 만들어두었던 search() 메서드를 이용하면 노드를 쉽게 얻을 수 있다.

### 3. remove(Object value) 메서드

&nbsp;&nbsp;&nbsp;remove(Object value) 메서드는 사용자가 원하는 특정 요소(value)를 리스트에서 찾아서 삭제하는 것이다.   
remove(int index) 메서드하고 동일한 메커니즘으로 작동한다. 다만 고려해야 할 점은 '삭제하려는 요소가 존재하는지'를 생각해야 한다. 삭제하려는 요소를 찾지 못했을 경우 false를 반환해주고, 찾았을 경우 remove(int index)와 동일한 삭제 방식을 사용하면 된다.

```java
@Override
public boolean remove(Object value) {

	Node<E> prevNode = head;
	boolean hasValue = false;
	Node<E> x = head;	// removedNode 

	// value와 일치하는 노드 검색
	for (; x != null; x = x.next) {
		if (value.equals(x.data)) {
			hasValue = true;
			break;
		}

		prevNode = x;
	}

	// 일치하는 요소가 없을 경우 false 반환
	if(x == null) {
		return false;
	}

	// 만약 삭제하려는 노드가 head라면 기존 remove()를 사용 	
	if (x.equals(head)) {
		remove();
		return true;
	}	else {
		// 이전 노드의 링크를 삭제하려는 노드의 다음 노드로 연결
		prevNode.next = x.next;
		
		x.data = null;
		x.next = null;
		size--;

		return true;
	}
}
```

## get, set, indexOf, contains 메서드 구현

&nbsp;&nbsp;&nbsp;부가기능이지만 매우 중요한 메서드 몇 개를 구현해보자.

### 1. get(int index) 메서드

&nbsp;&nbsp;&nbsp;get()은 index로 들어오는 값을 인덱스 삼아 해당 위치에 있는 요소를 반환하는 메서드다. 그런데 생각해보면 search() 메서드를 구현해놓았다. 이를 이용하면 쉽지않을까?  
이 둘은 약간의 차이점이 있는데 search() 메서드는 '노드'를 반환하고, get() 메서드는 '노드의 데이터'를 반환한다는 것이다. 즉 아래와 같이 손쉽게 구현할 수 있다.

```java
@Override
public E get(int index) {
	return search(index).data;
}
```

그리고 search() 내부에서 잘못된 위치일 경우 예외를 던지기 때문에 따로 예외처리를 해줄 필요는 없다.

### 2. set(int index, E value) 메서드

&nbsp;&nbsp;&nbsp;set 메서드는 기존에 index에 위치한 데이터를 새로운 데이터(value)으로 '교체'하는 것이다. add메서드가 데이터 '추가'이면 set은 '교체'이다.  
결과적으로 index에 위치한 데이터를 교체하는 것이기 때문에 마찬가지로 search() 메서드를 사용하여 노드를 찾아내고, 해당 노드의 데이터만 새로운 데이터로 변경해주면 된다.

```java
@Override
public void set(int index, E value) {
 
	Node<E> replaceNode = search(index);
	replaceNode.data = null;
	replaceNode.data = value;
}
```

마찬가지로 잘못된 인덱스를 참조하고 있진 않은지 검사가 필요하다. 그러나 search() 메서드 안에서 인덱스 검사를 해주기 때문에 따로 구현을 하지 않는다.

---
**Reference**

- [공식 문서](https://docs.oracle.com/javase/8/docs/api/)
- <https://st-lab.tistory.com/167>

---
<a name="footnote_1">1</a> :