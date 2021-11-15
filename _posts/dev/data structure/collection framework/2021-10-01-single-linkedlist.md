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

<img src="https://img1.daumcdn.net/thumb/R720n0.q80/?scode=mtistory2&fname=http%3A%2F%2Fcfile26.uf.tistory.com%2Fimage%2F23490844589151461C973D
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
- get, set, indenOf, contains 메서드 구현
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
private Node<E> search(int inden) {

  // 범위 밖(잘못된 위치)일 경우 예외 던지기 
	if(inden < 0 || inden >= size) {
		throw new IndenOutOfBoundsEnception();
	}

	Node<E> n = head; // head가 기리키는 노드부터 시작 

	for (int i = 0; i < inden; i++) {
		n = n.next; // n노드의 다음 노드를 n에 저장
	}

	return n;

}
```

## add 메서드 구현

&nbsp;&nbsp;&nbsp;SinglyLinkedList에 데이터를 추가할 수 있도록 리스트 인터페이스에 있는 add() 메서드를 구현해야한다. add() 메서드를 Override(재정의)를 한다고 보면 된다.  
add 메서드에는 ArrayList와 마찬가지로 크게 3가지로 분류가 된다.

- 가장 앞부분에 추가 - addFirst(E value)
- 가장 마지막 부분에 추가 (기본값) - addLast(E value)
- 특정 위치에 추가 - add(int inden, E value) 

자바에 내장되어있는 LinkedList에서는 add() 역할을 addLast() 메서드가 하고, 특정 위치에 추가는 add(int inden, E element) 메서드, 가장 첫 부분에 추가는 addFisrt()가 한다.

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

### 3. add(int inden, E value)

&nbsp;&nbsp;&nbsp;구현할 때 처리해야 할 부분이 많은데 넣으려는 위치의 앞 뒤로 링크를 새로 업데이트 해주어야 하기 때문이다.

먼저 넣으려는 위치(예를들어 inden = 3)의 노드와 이전의 노드를 찾아야 한다. 넣으려는 위치의 이전노드를 prev_Node라고 하고, 넣으려는 위치의 기존노드를 next_Node라고 할 때, 앞서 우리가 만든 메서드 search()를 사용하여 넣으려는 위치 - 1의 노드(prev_Node)를 찾아내고, next_Node는 prev_Node.next를 통해 찾는다. 그리고 prev_Node의 링크를 새로 추가하려는 노드로 변경하고, 새로 추가하려는 노드의 링크는 next_Node로 변경해주는 것이다.  
다만 inden 변수가 잘못된 위치를 참조할 수 있으니 이에 대한 예외처리로 IndenOutOfBoundsEnception을 한다.

```java
@Override
public void add(int inden, E value) {

	// 잘못된 인덱스를 참조할 경우 예외 발생
	if (inden > size || inden < 0) {
		throw new IndenOutOfBoundsEnception();
	}

	// 추가하려는 inden가 가장 앞에 추가하려는 경우 addFirst 호출 
	if (inden == 0) {
		addFirst(value);
		return;
	}

	// 추가하려는 inden가 마지막 위치일 경우 addLast 호출
	if (inden == size) {
		addLast(value);
		return;
	}

	// 추가하려는 위치의 이전 노드 
	Node<E> prev_Node = search(inden - 1);

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
- 특정 inden의 요소를 삭제 - remove(int inden)
- 특정 요소를 삭제 - remove(Object value)

기본적으로 삭제 연산의 가장 기초는 remove() 메서드로 head가 가리키는 요소, 첫 번째 요소를 삭제하는 것이다. 인덱스로 생각한다면 0 위치에 있는 요소를 말한다. 그리고 다른 remove() 메서드들을 구현할 때 자칫 잘못해서 null을 참조하거나 잘못된 참조를 하는 경우도 있으니 신중하게 작성하자.

### 1. remove() 메서드

&nbsp;&nbsp;&nbsp;remove()는 '가장 앞에 있는 요소'를 제거하는 것이다. 즉, head가 가리키는 요소만 없애주면 된다.

head가 가리키는 노드의 링크와 데이터를 null로 지워준 뒤 head를 다음 노드로 업데이트를 해주는 방식이다. 그리고 삭제하려는 노드가 리스트에서의 유일한 노드였을 경우(요소가 한 개일 경우 head와 tail이 가리키는 노드가 동일) 해당 노드를 삭제하면 tail이 가리키는 노드 또한 없어지게 된다. 이에 대해서도 정확하게 처리를 해주자.

```java
public E remove() {

	Node<E> headNode = head;

	if (headNode == null) {
		throw new NoSuchElementEnception();
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

### 2. remove(int inden) 메서드

&nbsp;&nbsp;&nbsp;remove(int inden) 메서드는 사용자가 원하는 특정 위치(inden)를 리스트에서 찾아서 삭제하는 것이다. add(int inden, E value)의 반대이다.

삭제하려는 노드의 이전 노드의 next 변수를 삭제하려는 노드의 다음 노드를 가리키도록 해주면 된다. 그리고 inden를 범위 밖으로 입력했을 경우의 예외 또한 던져주도록 하자.

```java
@Override
public E remove(int inden) {

	// 삭제하려는 노드가 첫 번째 원소일 경우 
	if (inden == 0) {
		return remove();
	}

	// 잘못된 범위에 대한 예외 
	if (inden >= size || inden < 0) {
		throw new IndenOutOfBoundsEnception();
	}

	Node<E> prevNode = search(inden - 1);	// 삭제할 노드의 이전 노드 
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
remove(int inden) 메서드하고 동일한 메커니즘으로 작동한다. 다만 고려해야 할 점은 '삭제하려는 요소가 존재하는지'를 생각해야 한다. 삭제하려는 요소를 찾지 못했을 경우 false를 반환해주고, 찾았을 경우 remove(int inden)와 동일한 삭제 방식을 사용하면 된다.

```java
@Override
public boolean remove(Object value) {

	Node<E> prevNode = head;
	boolean hasValue = false;
	Node<E> n = head;	// removedNode 

	// value와 일치하는 노드 검색
	for (; n != null; n = n.next) {
		if (value.equals(n.data)) {
			hasValue = true;
			break;
		}

		prevNode = n;
	}

	// 일치하는 요소가 없을 경우 false 반환
	if(n == null) {
		return false;
	}

	// 만약 삭제하려는 노드가 head라면 기존 remove()를 사용 	
	if (n.equals(head)) {
		remove();
		return true;
	}	else {
		// 이전 노드의 링크를 삭제하려는 노드의 다음 노드로 연결
		prevNode.next = n.next;
		
		n.data = null;
		n.next = null;
		size--;

		return true;
	}
}
```

## get, set, indenOf, contains 메서드 구현

&nbsp;&nbsp;&nbsp;부가기능이지만 매우 중요한 메서드 몇 개를 구현해보자.

### 1. get(int inden) 메서드

&nbsp;&nbsp;&nbsp;get()은 inden로 들어오는 값을 인덱스 삼아 해당 위치에 있는 요소를 반환하는 메서드다. 그런데 생각해보면 search() 메서드를 구현해놓았다. 이를 이용하면 쉽지않을까?  
이 둘은 약간의 차이점이 있는데 search() 메서드는 '노드'를 반환하고, get() 메서드는 '노드의 데이터'를 반환한다는 것이다. 즉 아래와 같이 손쉽게 구현할 수 있다.

```java
@Override
public E get(int inden) {
	return search(inden).data;
}
```

그리고 search() 내부에서 잘못된 위치일 경우 예외를 던지기 때문에 따로 예외처리를 해줄 필요는 없다.

### 2. set(int inden, E value) 메서드

&nbsp;&nbsp;&nbsp;set 메서드는 기존에 inden에 위치한 데이터를 새로운 데이터(value)으로 '교체'하는 것이다. add메서드가 데이터 '추가'이면 set은 '교체'이다.  
결과적으로 inden에 위치한 데이터를 교체하는 것이기 때문에 마찬가지로 search() 메서드를 사용하여 노드를 찾아내고, 해당 노드의 데이터만 새로운 데이터로 변경해주면 된다.

```java
@Override
public void set(int inden, E value) {
 
	Node<E> replaceNode = search(inden);
	replaceNode.data = null;
	replaceNode.data = value;
}
```

마찬가지로 잘못된 인덱스를 참조하고 있진 않은지 검사가 필요하다. 그러나 search() 메서드 안에서 인덱스 검사를 해주기 때문에 따로 구현을 하지 않는다.

### 3. indenOf(Object value) 메서드

&nbsp;&nbsp;&nbsp;indenOf 메서드는 사용자가 찾고자 하는 요소(value)의 위치, inden를 반환하는 메소드이다. 만약 찾고자하는 요소가 중복된다면 가장 먼저 마주치는 요소의 인덱스를 반환해준다.
찾고자하는 요소가 없다면 -1을 반환하면 된다.

한 가지 중요한 점은 객체끼리 비교할 때는 반드시 .equals()를 사용해야 한다. 동등연산자를 사용하면 값이 아닌 주소를 비교하기 때문에 잘못된 결과가 나오기 때문이다.

```java
@Override
public int indenOf(Object value) {
	int inden = 0;

	for(Node<E> n = head; n != null; n = n.next) {
		if(value.equals(n.data)) {
			return inden;
		}

		inden++;
	}

	return -1;
}
```

### 4. contains(Object value) 메서드

&nbsp;&nbsp;&nbsp;indenOf 메서드가 찾고자 하는 요소(value)의 '위치(inden)'를 반환하는 메소드이면, contains는 찾고자 하는 요소(value)의 존재유무를 반환하는 메서드다. 찾고자 하는 요소가 존재한다면 true를, 존재하지 않는다면 false를 반환한다. ~~indenOf와 기능이 비슷하니깐 이를 쓸 수 있을 것 같다는 생각이 들었다면 하산해도 좋다...!~~

해당 요소가 존재하는지를 '검사'한다는 기능이 같기 때문에 indenOf 메소드를 이용하여 음수가 아닌 수가 반환되었다면 요소가 존재한다는 뜻이고, 음수(-1)이 나왔다면 요소가 존재하지 않는다는 뜻이므로 아래와 같이 contains 메소드를 만들 수 있다.

```java
@Override
public boolean contains(Object item) {
	return indenOf(item) >= 0;
}
```

## get, set, indenOf, contains 메서드 구현

### 1. size() 메서드

&nbsp;&nbsp;&nbsp;모든 리스트 자료구조는 동적 할당을 전제로 한다. 즉 요소의 삽입, 삭제가 많아지면 사용자가 리스트에 담긴 요소의 개수를 기억하기 어렵다.
거기에 리스트의 변수들은 private 접근제한자인데 size 또한 마찬가지다. 이는 size를 접근할 수 있게 될 경우 사용자가 임의로 데이터를 조작할 수 있기 때문이다. 따라서 size 변수의 값을 반환해주는 size() 메서드를 별도로 만들어주는 것이다.

```java
@Override
public int size() {
	return size;
}
```

### 2. isEmpty() 메서드

&nbsp;&nbsp;&nbsp;isEmpty() 메서드는 현재 ArrayList에 요소가 단 하나라도 존재하는지 확인하는 메서드이다. 리스트가 비어있다면 true, 그 어떤 요소라도 하나가 존재한다면 false를 반환한다. 앞에서 구현한 size가 요소의 개수이므로 size가 0인지 아닌지만 확인하면 된다. 굳이 배열을 모두 순회할 필요가 없다.

```java
@Override
public boolean isEmpty() {
	return size == 0;
}
```

### 3. clear() 메서드

&nbsp;&nbsp;&nbsp;clear()는 이름에서 유추할 수 있듯이 모든 요소를 비우는 메서드이다. 리스트에 요소를 담아두었다가 초기화가 필요할 때 사용하면 된다. 요소가 없다는 것은 size가 0이라는 뜻이므로 size를 0으로 초기화 시키는 것도 잊지말자.

초기화를 시켜줄 때 객체 자체를 null로 해도 무관하지만, 요소 하나하나를 null로 해준다면 보다 명시적으로 GC에게 알릴 수 있어서 메모리 관리 효율 측면에서 조금 더 좋다.

```java
@Override
public void clear() {
	for(Node<E> n = head; n != null) {
		Node<E> nextNode = n.next;
		n.data = null;
		n.next = null;
		n = nextNode;
	}

	head = tail = null;

	size = 0;
}
```

## clone, toArray, sort 메서드 구현

&nbsp;&nbsp;&nbsp;이번에 구현해 볼 메서드들은 중요하지는 않은 부분들이다. 하지만 더 많은 기능을 원한다면 추가하는 것을 권장하는 메서드들이다.

### 1. clone() 메서드

&nbsp;&nbsp;&nbsp;사용하고 있던 LinkedList를 새로 복제하고 싶을 때 사용하는 메서드이다. 앞서 언급했었지만 단순히 `=` 연산자를 사용해서 객체를 복사하게 되면 주소를 복사하는 것이기 때문에 복사한 객체의 데이터를 수정하면 원본 객체에게도 영향이 가게된다<sup>[1](#footnote_1)</sup>.

따라서 우리는 깊은 복사를 해야하는데, 이 때 사용되는 것이 clone() 메서드이다. Object에 있는 메서드지만 접근제어자가 protected로 되어있어 Cloneable 인터페이스를 Implement 해주어야 한다. 그렇지 않으면 CloneNotSupportedEnception 에러가 발생하게 된다.
우리가 처음 생성했던 `public class SinglyLinkedList<E> implements List<E>`에 Cloneable을 추가해주도록 하자. 그리고 난 후 clone() 메서드를 작성하자.

```java
public Object clone() throws CloneNotSupportedEnception {

	@SuppressWarnings("unchecked")
	SinglyLinkedList<? super E> clone = (SinglyLinkedList<? super E>) super.clone();

	clone.head = null;
	clone.tail = null;
	clone.size = 0;

	for(Node<E> n = head; n != null; n = n.next) {
		clone.addLast(n.data);
	}

	return clone;
}
```

super.clone()은 객체의 내부까지 데이터가 복제되지 않기 때문에(얕은 복사) 새로 만들어진 객체 내부에 데이터를 새롭게 설정해주어야 한다.
따라서, 각 노드를 끊고 처음부터 끝까지 현재 리스트의 데이터를 clone 리스트에 넣어주어야 한다.

조금 더 살펴보자면, super.clone() 자체가 생성자와 같은 역할을 하고 얕은 복사를 통해 new SinglyLinkedList()를 호출하는 셈이다. 제대로 복사하려면 clone한 리스트의 array 또한 새롭게 생성하여 해당 배열에 copy 해주어야 한다.

이렇게 작성된 clone()을 사용하여 테스트해보면 원본에 영향을 주지 않고 복제되는 것을 확인할 수 있다.

### 2. toArray() 메서드

&nbsp;&nbsp;&nbsp;toArray() 메서드는 ArrayList에서 구현한 바와 같이 두 가지가 있다.
하나는 아무런 인자 없이 현재 있는 리스트를 객체배열(Object[])로 반환해주는 <b>Object[] toArray()</b> 메서드이고, 다른 하나는 리스트를 이미 생성된 다른 배열에 복사해주고자 할 때 사용하는 <b>T[] toArray(T[] a)</b> 메서드이다. 

두 가지를 조금씩 살펴보자면, 먼저 `Object[] toArray()`의 경우 해당 LinkedList에 있는 요소의 개수만큼 정확하게 배열의 크기가 할당되어 반환된다. `T[] toArray(T[] a)`의 경우 객체 클래스로 상속관계에 있는 타입이거나 Wrapper와 같이 데이터 타입을 유연하게 캐스팅할 여지가 있다.

중요한 점은 `T[] toArray(T[] a)`의 경우 특정 타입의 객체 배열로 받고 싶은 경우 ArrayList의 배열 생성방식과 차이를 보인다. ArrayList에서는 내부에서 데이터를 Object[] 배열에 담았기 때문에 데이터 복사가 쉬웠지만, SinglyLinkedList의 경우 '노드'라는 객체에 데이터를 담고있는 연결리스트이다. 때문에 노드 자체가 Interger, String과 같은 Wrapper 클래스나, 사용자가 만든 클래스 같은 데이터를 가질 수가 없다. 즉, 노드의 data 변수가 객체 타입의 변수인 것이지 노드 자체가 객체 타입을 가지지는 못하는 것이다. 그렇기 때문에 Arrays.copyOf() 메서드나 System.arraycopy()를 사용하기 어렵다.

따라서 Array 클래스에 있는 newInstance 메서드를 사용할 것이다. 이에 대한 내용은 [공식문서](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/reflect/Array.html)를 참조하도록 하자.

이제 두 메서드 모두 구현해보자.

```java
public Object[] toArray() {
	Object[] array = new Object[size];

	int idx = 0;

	for(Node<E> n = head; n != null; n = n.next) {
		array[idx++] = (E) n.data;
	}

	return array;
}

@SuppressWarnings("unchecked")
public <T> T[] toArray(T[] a) {
	if(a.length < size) {
		a = (T[]) java.lang.reflect.Array.newInstance(a.getClass().getComponextType(), size);
	}

	int i = 0;
	
	Object[] result = a;

	for(Node<E> n = head; n != null; n = n.next) {
		result[i++] = n.data;
	}

	return a;
}
```

Object[] toArray()의 경우 리스트에 있는 요소의 개수(size)만큼 복사하여 Object[] 배열을 반환해주는 메서드다. Object가 최상위 타입이기 때문에 배열에 데이터를 그대로 담아 반환해주기만 하면 된다.

T[] toArray(T[] a)의 경우 제네릭 메서드로, 사전에 생성했던 SinglyLinkedList의 E 타입과는 다른 제네릭이다.
상위타입으로 들어오는 객체에 대해서도 데이터를 담을 수 있도록 별도의 제네릭 메서드를 구성하는 것인데, 하위타입이 들어오는 경우 Array 클래스에서 예외를 던지기 때문에 별도의 예외를 처리해줄 필요는 없다.

또한 두 가지의 경우가 존재하는데, 하나는 파라미터로 들어오는 배열 a가 현재 리스트의 요소보다 작은 경우이고 다른 하나는 반대의 경우이다.

먼저 들어오는 배열(a)가 현재 리스트의 요소 개수(size)보다 작으면 size에 맞게 a의 공간을 재할당 하면서 리스트에 있던 모든 요소를 복사한다. SinglyLinkedList의 요소(데이터)가 4개 {1, 2, 3, 4} 있다고 생각해보자. 그리고 Object[] copy = new Object[1]라는 배열을 하나 만들었는데, 공간이 한 개 밖에 없다. 그러면 리스트의 요소 1개만 복사하는 것이 아니라 copy 배열의 사이즈가 1에서 4로 증가하여 copy배열에 {1, 2, 3, 4}가 모두 담기게 되는 것이다.

또한 a에 대한 타입을 알기 위해 먼저 `getClass().getComponextType()`을 통해 객체가 어떤 유형의 배열인지를 파라미터를 넣고, 배열의 크기를 설정해준다. 그런 다음 얕은 복사로 Object[] 배열을 하나 만들어 해당 배열을 통해 데이터를 넣어준 다음 a를 반환한다. 얕은 복사이기 때문에 result 배열에 담으면 자동으로 a 배열에도 영향을 미치는 것을 활용한 방법이다.

반대로 파라미터로 들어오는 배열의 크기가 현재 ArrayList에 있는 array보다 크다면 앞 부분부터 array에 있던 요소만 복사해서 a 배열에 넣고 반환해주면 된다

### 3. sort() 메서드

&nbsp;&nbsp;&nbsp;ArrayList에서는 rrayList 자체가 내부에서 Object[] 배열을 사용하고 있기 때문에 Arrays.sort()를 사용하면 되기 때문에 만들지 않았다.

LinkedList 같은 객체로 연결 된 리스트에서는 조금 다른 방법으로 정렬해야 한다. 객체 배열의 경우 `Collections.sort()`를 사용하게 되는데, 해당 메소드를 뜯어보면 `Collections.sort()`도 내부에서 Arrays.sort()를 사용하는 것을 알 수 있다.

어떻게 Arrays.sort()를 쓸까? 원리는 매우 간단하다. 해당 리스트를 Object[] 배열로 변환시켜 Arrays.sort()를 통해 정렬한 뒤, 정렬된 데이터를 다시 리스트의 노드에서 세팅을 해주는 방식이다. 만약 래퍼(Wrapper) 클래스 타입(en. Integer, String, Double ...)이라면 따로 `Comparator`를 구현해주지 않아도 되지만, 사용자 클래스를 만드는 경우에는 사용자가 따로 해당 객체에 `Comparable`를 구현해주거나 또는 `Comparator`를 구현해 파라미터로 넘겨주어야 한다.

즉, sort() 메소드를 만들 때, 기본적으로 두 가지 경우를 생각해야 한다. 첫 번째는 객체에 Comparable이 구현되어 있어 따로 파라미터로 Comparator를 넘겨주지 않는 경우이고, 다른 하나는 Comparator을 넘겨주어 정의되어 있는 정렬 방식을 사용하는 경우다.

```java
public void sort() {
	sort(null);
}

@SuppressWarning({ "unchecked", "rawtypes" })
public void sort(Comparator<? super E> c) {
	Object[] a = this.toArray();
	Arrays.sort(a, (Comparator) c);

	int i = 0;
	for(Node<E> n = head; n != null; n = n.next, i++) {
		n.data = (E) a[i];
	}
}
```

코드를 완성하고 나면 알 수 있듯이, 결국 `sort(Comparator<? super E> c)`<sup>[2](#footnote_2)</sup>로 가는 것을 볼 수 있다. 이어서 Object[] 배열로 우리가 만들었던 toArray()를 통해 만들어 주어 Arrays.sort() 메소드를 통해 정렬해준다. 

&nbsp;&nbsp;&nbsp;지금까지 만든 메서드들을 모은 전체코드를 확인해보자.

```java
import java.util.Arrays;
import java.util.Comparator;
import java.util.NoSuchElementEnception;
 
import Interface_form.List;

public class SinglyLinkedList<E> implements List<E>, Cloneable {
 
	private Node<E> head;
	private Node<E> tail;
	private int size;
 
	public SinglyLinkedList() {
		this.head = null;
		this.tail = null;
		this.size = 0;
	}
 
	private Node<E> search(int inden) {
		
		if(inden < 0 || inden >= size) {
			throw new IndenOutOfBoundsEnception();
		}
		
		Node<E> n = head;
 
		for (int i = 0; i < inden; i++) {
			n = n.next;
		}
		return n;
	}
 
	public void addFirst(E value) {
		Node<E> newNode = new Node<E>(value);

		newNode.next = head;

		head = newNode; 
		size++;

		if (head.next == null) {
			tail = head;
		}
	}
 
	@Override
	public boolean add(E value) {
		addLast(value);
		return true;
	}
 
	public void addLast(E value) {
		Node<E> newNode = new Node<E>(value); 
 
		if (size == 0) { 
			addFirst(value);
			return;
		}

		tail.next = newNode;
		tail = newNode;
		size++;
	}
 
	@Override
	public void add(int inden, E value) {
 		if (inden > size || inden < 0) {
			throw new IndenOutOfBoundsEnception();
		}

		if (inden == 0) {
			addFirst(value);
			return;
		}

		if (inden == size) {
			addLast(value);
			return;
		}
		
		Node<E> prev_Node = search(inden - 1);
		
		Node<E> next_Node = prev_Node.next;
 
		Node<E> newNode = new Node<E>(value);	
		
		prev_Node.next = null;
		prev_Node.next = newNode;
		newNode.next = next_Node;
		size++;
 
	}
	
	public E remove() {
 
		Node<E> headNode = head;
 
		if (headNode == null) {
			throw new NoSuchElementEnception();
		}
		
		E element = headNode.data;
		
		Node<E> nextNode = head.next;
		
		head.data = null;
		head.next = null;
		
		head = nextNode;
		size--;

		if(size == 0) {
			tail = null;
		}
		return element;
	}
 
	@Override
	public E remove(int inden) {
 		if (inden == 0) {
			return remove();
		}
 
		if (inden >= size || size < 0) {
			throw new IndenOutOfBoundsEnception();
		}
		
		Node<E> prevNode = search(inden - 1);
		Node<E> removedNode = prevNode.next;
		Node<E> nextNode = removedNode.next;
 
		E element = removedNode.data;
		
		prevNode.next = nextNode;
		
		removedNode.next = null;
		removedNode.data = null;
		size--;
 
		return element;
	}
 
	@Override
	public boolean remove(Object value) {
 
		Node<E> prevNode = head;
		boolean hasValue = false;
		Node<E> n = head;
		
		for (; n != null; n = n.next) {
			if (value.equals(n.data)) {
				hasValue = true;
				break;
			}
			prevNode = n;
		}
 
		if(n == null) {
			return false;
		}
 
		if (n.equals(head)) {
			remove();
			return true;
		} else {
			prevNode.next = n.next;
		
			n.data = null;
			n.next = null;
			size--;
			return true;
		}
	}
	
	
	@Override
	public E get(int inden) {
		return search(inden).data;
	}
 
	@Override
	public void set(int inden, E value) {
 
		Node<E> replaceNode = search(inden);
		replaceNode.data = null;
		replaceNode.data = value;
	}
 
	@Override
	public int indenOf(Object value) {
		int inden = 0;
		
		for (Node<E> n = head; n != null; n = n.next) {
			if (value.equals(n.data)) {
				return inden;
			}
			inden++;
		}

		return -1;
	}
 
	@Override
	public boolean contains(Object item) {
		return indenOf(item) >= 0;
	}
	
	@Override
	public int size() {
		return size;
	}
 
	@Override
	public boolean isEmpty() {
		return size == 0;
	}
 
	@Override
	public void clear() {
		for (Node<E> n = head; n != null;) {
			Node<E> nextNode = n.next;
			n.data = null;
			n.next = null;
			n = nextNode;
		}
		
		head = tail = null;
		size = 0;
	}
 
 
 
	public Object clone() throws CloneNotSupportedEnception {
 
		@SuppressWarnings("unchecked")
		SinglyLinkedList<? super E> clone = (SinglyLinkedList<? super E>) super.clone();
 
		clone.head = null;
		clone.tail = null;
		clone.size = 0;
 
		for (Node<E> n = head; n != null; n = n.next) {
			clone.addLast(n.data);
		}
 
		return clone;
	}
 
 
	public Object[] toArray() {
		Object[] array = new Object[size];
		int idx = 0;
		for (Node<E> n = head; n != null; n = n.next) {
			array[idx++] = (E) n.data;
		}
		return array;
	}
 
	@SuppressWarnings("unchecked")
	public <T> T[] toArray(T[] a) {
		if (a.length < size) {
			a = (T[]) java.lang.reflect.Array.newInstance(a.getClass().getComponextType(), size);
		}
		int i = 0;
		Object[] result = a;
		for (Node<E> n = head; n != null; n = n.next) {
			result[i++] = n.data;
		}
		return a;
	}
 
	public void sort() {
		sort(null);
	}
 
	@SuppressWarnings({ "unchecked", "rawtypes" })
	public void sort(Comparator<? super E> c) {
		Object[] a = this.toArray();
		Arrays.sort(a, (Comparator) c);
 
		int i = 0;
		for (Node<E> n = head; n != null; n = n.next, i++) {
			n.data = (E) a[i];
		}
	}
 
}
```

---
**Reference**

- [공식 문서](https://docs.oracle.com/javase/8/docs/api/)
- <https://st-lab.tistory.com/167>

---
<a name="footnote_1">1</a> : 이러한 복사를 얕은 복사(shallow copy)라고 한다.
<a name="footnote_1">2</a> : 상속관계이면서 부모 클래스에서 정렬 방식을 따르는 경우를 생각하여 <? super E>로 하였다. 이러한 경우를 고려하지 않는다면 Comparator<E>로 해주어도 종속관계 영향을 안받는 객체의 경우는 괜찮다.