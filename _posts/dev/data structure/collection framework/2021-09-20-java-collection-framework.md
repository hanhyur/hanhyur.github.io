---
layout: post
date: 2021-09-20 22:26:00
title: "자바 컬렉션 프레임워크"
description: "자료구조"
subject: dev
category: [ cs ]
tags: [ cs, data structure, collection framework ]
use_math: true
comments: true
---

# 시작

&nbsp;&nbsp;&nbsp;아마 프로그래밍을 전공하거나 관련된 전공이라면 '자료구조'라는 단어를 들어본 적이 있을 것이다. 자료구조는 Data Structure라고 하는데 직역하면 데이터 구조, 좀 더 자세하게 설명하자면 '일련의 일정 타입들의 데이터 모임 또는 관계를 나타낸 구성체'라고 말할 수 있다.

&nbsp;&nbsp;&nbsp;특히 알고리즘 문제를 풀어낼 때 자료구조와 알고리즘은 서로 뗄 수 없는 의존적인 관계이기 때문에 자료구조에 대한 이해가 있어야 한다. "왜 상호의존적이냐?"라고 묻는다면 어떤 알고리즘 문제를 풀기 위해 문제를 해석한 다음 보통 자료구조를 선택한다. 자료구조를 선택하면 해당 자료구조에 맞는 알고리즘을 선택하는데 보다 더 효율적인 알고리즘을 선택할 수 있다는 장점이 있다. (물론 선후 관계가 바뀔 수도 있다.)

&nbsp;&nbsp;&nbsp;예를 들어 순서가 있는 데이터들이 있을 때 삽입(insert/add), 삭제(remove/delete)가 빈번하게 발생한다면 LinkedList를, 아닐 경우에는 ArrayList를 쓰듯이 각 자료구조별로 장단점을 갖고 있기 때문에 알고리즘 선택에 있어서 매우 중요한 역할을 담당한다.  
이렇듯 자료구조는 프로그래밍의 가장 기본이기 때문에 많은 언어들에서도 표준 라이브러리로 다양한 자료구조를 지원하고 있다.

&nbsp;&nbsp;&nbsp;이러한 자료구조의 이해를 돕기 위해 이번에 자바의 대표적인 자료구조인 Collection을 학습하고 이와 관련한 자료구조들을 직접 구현해보려 한다.

# 자료구조의 분류

&nbsp;&nbsp;&nbsp;자료구조 분류법은 많은 분류법이 있지만, 대표적으로 많이 분류되는 방법은 선형 자료구조(Linear Data Structure)과 비선형 자료구조(Nonlinear Data Structure)로 나눌 수 있다. 이러한 분류를 보통 '형태에 따른 자료구조'라고 보고, 각 자료구조에 알맞게 구체화 된 것들을 '구현된 자료구조'이라고 한다. 물론 언어별로 관점이 조금씩 달라서 'A는 B다'라고 딱 떨어지게 정의할 수는 없으니 대략적으로만 이해하면 될 것이다.

&nbsp;&nbsp;&nbsp;먼저 선형 자료구조(Linear Data Structure)에 대해 알아보자.  
선형 자료구조는 쉽게 생각해서 '데이터가 일렬로 연결된 형태'라고 보면 된다. 우리가 흔히 쓰는 int[] 배열같은 것을 생각하면 쉽다. 선형 자료구조는 대표적으로 리스트(List)와 큐(Queue), 덱(Deque)이 있다. 

&nbsp;&nbsp;&nbsp;비선형 자료구조(Nonlinear Data Structure)는 선형 자료구조의 반대다. 일렬로 나열된 것이 아닌, 각 요소가 여러 개의 요소와 연결 된 형태를 생각하면 된다. 거미줄 같은 형태를 생각하면 된다. 대표적인 비선형 자료구조는 그래프(Graph)와 트리(Tree)가 있다.

&nbsp;&nbsp;&nbsp;그리고 앞으로 설명 할 자료구조 중 위 두 가지 분류에 해당되지 않는 자료구조로 집합(Set)이 있다. 보통 기타 자료구조 또는 집합 자료구조로 본다. 집합의 경우는 데이터가 연결 된 형식이 아니다. 집합(set)은 table에 가까운 자료구조라고 생각할 수 있다.  
그 외에 파일 자료구조도 있는데, 이 것은 방향성이 조금 다르기 때문에 순차파일, 색인파일, 직접파일이 있다는 정도만 알아두자.

# Java Collections FrameWork

&nbsp;&nbsp;&nbsp;이제 우리가 학습할 Java Collections FrameWork의 의미부터 이해해보자. Java는 말 그대로 자바 언어를 의미하고, Collections는 '어떤 일정한 부류의 것을 수집하여 한 공간에 모아놓은 것'을 컬렉션(Collection)이라 한다. 예를 들어 동전 수집이라던가 우표 수집 등이 있다. 이처럼 Java에서 비슷한 류의 데이터들을 쉽게 다루기 위해 모아놓은 것들을 가공 및 처리 할 수 있도록 지원하는 자료구조라고 생각할 수 있겠다.

&nbsp;&nbsp;&nbsp;그렇다면 Framework는 무슨 뜻일까? frame이라는 단어에서 딱 떠오르는 것이 틀(뼈대)이다. 건물을 짓는 공사장을 보면 땅 위에 철근들이 여기저기 박혀있고 주변을 나무로 감싸서 전체적인 틀을 만든다. 사람으로 치자면 뼈에 해당하는 부분이고 이렇듯 Framework란 어떤 문제를 해결하기 위한 구조의 뼈대가 되는 기본 구조를 의미한다.

&nbsp;&nbsp;&nbsp;이제 단어들을 정리하면, <b>일정 타입의 데이터들이 모여 쉽게 가공 할 수 있도록 지원하는 자료구조들의 뼈대(기본 구조)</b>라는 의미가 된다.

&nbsp;&nbsp;&nbsp;기본 구조라고 할 때 떠오르는 것이 있을 것이다. 바로 Interface(인터페이스)다. 인터페이스 자체가 기본적인 뼈대(추상 구조)로 되어있지 않은가? 이렇듯 실제로 자바에서 제공하는 Collection은 크게 3가지 인터페이스, List(리스트), Queue(큐), Set(집합)으로 나뉘어 있다. 앞서 설명한 '형태에 따른 자료구조'라고 보면 된다. 그리고 각 분야별로 '구현' 된 것들이 있다.  
말로 하면 이해하기 힘들 것이니 아래 이미지를 보자.

<img src="https://miro.medium.com/max/1200/1*YXafPhPiXmGrSM42D0aX6Q.png" width="60%" height="auto" align="center"><br/>

파란 칸들은 클래스이고, 주황색 칸들은 인터페이스들이다. 각 관계에서 클래스가 인터페이스를 향하는 것은 대부분 구현(implements)관계이고 인터페이스 간은 확장(extends) 관계이다. 예외적으로 stack과 vector는 구현관계에 있다. 

&nbsp;&nbsp;&nbsp;아무튼 이전 파트에서 설명했던 것을 적용해보면 List, Queue, Set. 이 3가지의 형태에 따른 자료구조들이 있다. 그리고 Queue와 Set에는 조금 더 구체화 되어 Deque과 SortedSet이라는 형태에 따른 자료구조가 있는 것이다. 그리고 이 형태에 따른 자료구조들은 각각 '구현'이 되어 class로 제공된다. 파란 부분이 '구현된 자료구조'라고 보면 된다. 자바에서 Interface를 class파일에서 쓰면 보통 '구현한다'라고 한다.

&nbsp;&nbsp;&nbsp;여기서 맨 위의 Iterable이 뭐지 싶을 것이다. Iteration이라는 단어가 사전에 등제되어 있는지는 명확히 모르겠지만 컴퓨터 쪽에서는 '반복', '되풀이'를 의미한다. 즉, Iterable 이라 한다면 '반복 가능한' 이라는 정도로 이해하면 될 것이다.

&nbsp;&nbsp;&nbsp;그럼 Collection Interface 상위에 왜 Iterable이 있을까? 저기서 제공하고 있는 class들은 모두 객체 형태로 내부 구현 또한 대개 Object[] 배열 형태가 아니라 각각의 객체를 갖고 움직인다. 그래서 객체의 데이터들을 모두 순회하면서 출력하려면 사용자들이 각각의 데이터 순회 방법을 알거나 하나씩 get() 같은 메소드를 통해 데이터를 하나씩 꺼내와야 한다.  
하지만 Iterable에서는 인터페이스를 보면 알겠지만 for-each를 제공한다. 따라서 Iterable 인터페이스를 쓰는 모든 클래스들은 기본적으로 for-each 문법을 쉽게 사용할 수 있다. 즉, 반복자로 구현되어 나오게 하는 것이다.

&nbsp;&nbsp;&nbsp;이제 Collection에서 List, Queue, Set에 대해 간략히 알아보자.

# List 리스트

&nbsp;&nbsp;&nbsp;List Interface는 대표적인 선형 자료구조로 주로 순서가 있는 데이터를 목록으로 이용할 수 있도록 만들어진 인터페이스다. 쉽게 얘기하면 우리가 배열에서 쓸 때 `int[] array = new int[10];`처럼 쓴다. 하지만, 이 처럼 선언한 배열의 경우 10개의 공간 외에는 더이상 사용하지 못한다. 즉, `array[13] = 32;`라고 해주더라도 할당된 크기(범위) 밖이기 때문에 `IndexOutofBoundsException`라는 에러가 발생한다.

&nbsp;&nbsp;&nbsp;이러한 단점을 보완하여 List를 통해 구현된 클래스들은 '동적 크기'를 갖으며 배열처럼 사용할 수 있게 되어있다. 배열의 기능 + 동적 크기 할당이 합쳐진 셈이다.

## List Interface를 구현하는 클래스

1. ArrayList
2. LinkedList
3. Vector (+ Vector를 상속받은 Stack).

## List Interface에 선언된 대표적인 메소드

<table>
  <tr>
    <td><center> 메소드 </center></td>
    <td><center> 리턴 타입 </center></td>
    <td><center> 설 명 </center></td>
  </tr>
  <tr>
    <td><center> add(E e) </center></td>
    <td><center> boolean </center></td>
    <td> list에 요소를 추가. </td>
  </tr>
  <tr>
    <td><center> remove(Object o) </center></td>
    <td><center> boolean </center></td>
    <td> 지정한 객체(o)와 같은 첫 번째 객체를 삭제. </td>
  </tr>
  <tr>
    <td><center> contains(Object o) </center></td>
    <td><center> boolean </center></td>
    <td> 지정한 객체(o)가 컬렉션에 있는지 확인<br/>있는 경우 true, 없는 경우 false. </td>
  </tr>
  <tr>
    <td><center> isEmpty() </center></td>
    <td><center> boolean </center></td>
    <td> 현재 컬렉션에 요소가 없다면 true, 있다면 false. </td>
  </tr>
  <tr>
    <td><center> equals(Object o) </center></td>
    <td><center> boolean </center></td>
    <td> 지정된 객체와 같은지 비교. </td>
  </tr>
  <tr>
    <td><center> indexOf(Object o) </center></td>
    <td><center> int </center></td>
    <td> 지정된 객체가 있는 첫 번째 요소의 위치를 반환. 없을 경우 -1. </td>
  </tr>
  <tr>
    <td><center> size() </center></td>
    <td><center> int </center></td>
    <td> 현재 컬렉션에 있는 요소의 개수를 반환. </td>
  </tr>
  <tr>
    <td><center> get(int i) </center></td>
    <td><center> E </center></td>
    <td> 지정된 위치에 저장된 원소를 반환. </td>
  </tr>
  <tr>
    <td><center> set(int i, E elements) </center></td>
    <td><center> E </center></td>
    <td> 지정된 위치에 있는 요소를 지정된 요소로 변경. </td>
  </tr>
  <tr>
    <td><center> clear() </center></td>
    <td><center> void </center></td>
    <td> 모든 요소를 제거. </td>
  </tr>
</table>
<br/>

&nbsp;&nbsp;&nbsp;List를 구현하는 각 클래스들은 조금씩 특징이 다르다. 4가지 클래스를 간략하게 살펴보자.

&nbsp;&nbsp;&nbsp;ArrayList는 Object[] 배열을 사용하면서 내부 구현을 통해 동적으로 관리를 한다. 우리가 흔히 쓰는 primitive 배열(int[])과 유사한 형태라고 보면 된다.  
최상위 타입인 Object 타입으로 배열을 생성하여 사용하기 때문에 요소 접근(access elements)에서 뛰어난 성능을 보인다. 하지만 중간 요소가 삽입, 삭제가 일어나는 경우 그 뒤의 요소들은 한 칸씩 밀어야 하거나 당겨야 하기 때문에 삽입, 삭제에서는 비효율적이다.

&nbsp;&nbsp;&nbsp;LinkedList는 데이터(item)와 주소로 이루어진 클래스를 만들어 서로 연결하는 방식이다. 데이터와 주소로 이루어진 클래스를 Node(노드)라고 하는데, 각 노드는 이전의 노드와 다음 노드를 연결되어있다. 즉, 객체끼리 연결한 방식으로 요소를 검색해야 할 경우 처음 노드부터 찾으려는 노드가 나올 때 까지 연결된 노드들을 모두 방문해야한다는 점에서 성능이 떨어진다. 반면에 해당 노드를 삭제, 삽입해야 할 경우 해당 노드의 링크를 끊거나 연결만 해주면 되기 때문에 삽입, 삭제에서는 매우 좋은 효율을 보인다.

&nbsp;&nbsp;&nbsp;Vector는 자바에서는 자주 보이지 않는 클래스인데 기본적으로 ArrayList와 비슷하며 Object[] 배열을 사용해 요소 접근에서 빠른 성능을 보인다.  
그렇다면 Vector는 왜 있는 것일까? 원래 Vector는 Collection Framwork가 도입되기 전부터 지원하던 클래스로 항상 '동기화'를 지원한다. 그렇다보니 멀티 쓰레드에서는 안전하지만, 단일 쓰레드에서도 동기화를 하기 때문에 ArrayList에 비해 성능이 약간 느리다.

&nbsp;&nbsp;&nbsp;Stack은 쌓아 올리는 것이다. LIFO(Last in First out) 또는 후입선출이라고 하는데 짐을 쌓아올릴 때 가장 마지막에 쌓은 짐이 가장 위에 있을 것이다. 그리고 짐을 뺄 때도 가장 위에 있는 짐부터 빼게 될 것이다. 대표적인 예시로는 웹페이지 '뒤로가기'가 있다. 우리가 새로운 페이지로 넘어갈 때마다 넘어가기 전 페이지를 스택에 쌓고, 만약 뒤로가기를 누른다면 가장 위에 있는 페이지부터 꺼내오는 방식이다.  
참고로 Stack의 경우 Vector클래스를 상속받고 있고, java에서 지원하는 Stack 클래스의 메소드들도 뜯어보면 Vector에 있는 메소드를 이용하여 구현되어 있다. 

```java
ArrayList<T> arraylist = new ArrayList<>();
LinkedList<T> linkedlist = new LinkedList<>();
Vector<T> vector = new Vector<>();
Stack<T> stack = new Stack<>();
```

```java
List<T> arraylist = new ArrayList<>();
List<T> linkedlist = new LinkedList<>();
List<T> vector = new Vector<>();
List<T> stack = new Stack<>();

// Stack은 Vector를 상속하기 때문에 아래와 같이 생성할 수 있다.
Vector<T> stack = new Stack<>();
```

&nbsp;&nbsp;&nbsp;각각의 객체는 위와 같은 방식으로 선언할 수 있다. new 생성자 뒤에 객체를 명시해주기 때문에 두 번째 방식처럼 해주어도 괜찮지만 첫 번째 방식으로 해주는게 확인하기도 쉽고 가장 확실하다.

# Queue 큐

&nbsp;&nbsp;&nbsp;Queue Interface는 선형 자료구조로 주로 순서가 있는 데이터를 기반으로 '선입선출(先入先出, FIFO : First-in First-out)'을 위해 만들어진 인터페이스다. 흔히 Stack(스택)과 많이 비교를 하는 자료구조다. 큐가 동작하는 방식은 데이터를 10, 20, 30, 40 순으로 넣으면 데이터를 꺼낼 때(poll) 넣은 순서 그대로 10, 20, 30, 40이 나오는 구조이다. 이 때 가장 앞쪽에 있는 위치를 head(헤드)라고 부르고, 가장 후위(뒤)에 있는 위치를 tail(꼬리)라고 부른다.

&nbsp;&nbsp;&nbsp;Collection 구조를 보면 알겠지만, Queue를 상속하고 있는 Deque(덱)이라는 Interface도 있다. 둘 다 같은 부류인데 Queue는 한쪽 방향으로만(단방향) 삽입-삭제가 가능한 반면, Deque는 Double ended Queue라는 의미로 양쪽에서 삽입-삭제가 가능한 자료구조라 보면 된다. 즉, head에서도 접근 가능하며, tail에서도 접근 가능한 양방향 큐라고 보면 된다.

## Queue/Deque Interface를 구현하는 클래스

1. LinkedList
2. ArrayDeque
3. PriorityQueue

## Queue/Deque Interface에 선언된 대표적인 메소드

<table>
  <tr>
    <td><center> 메소드 </center></td>
    <td><center> 리턴 타입 </center></td>
    <td><center> 설 명 </center></td>
  </tr>
  <tr>
    <td><center> add(E e) </center></td>
    <td><center> boolean </center></td>
    <td> 요소를 꼬리에 추가. 큐가 가득 찼을 경우 illegalStateException 예외가 발생. </td>
  </tr>
  <tr>
    <td><center> offer(E e) </center></td>
    <td><center> boolean </center></td>
    <td> 요소를 꼬리에 추가. </td>
  </tr>
  <tr>
    <td><center> peek() </center></td>
    <td><center> E </center></td>
    <td> 헤드를 삭제하지 않고 요소를 검색 후 반환. </td>
  </tr>
  <tr>
    <td><center> poll() </center></td>
    <td><center> E </center></td>
    <td> 헤드를 검색 및 삭제하면서 요소를 반환. </td>
  </tr>
  <tr>
    <td><center> addFirst(Object o) </center></td>
    <td><center> void </center></td>
    <td> 요소를 헤드에 추가. 큐가 가득 찼을 경우 illegalStateException 예외가 발생. </td>
  </tr>
  <tr>
    <td><center> addLast() </center></td>
    <td><center> void </center></td>
    <td> 요소를 꼬리에 추가. 큐가 가득 찼을 경우 illegalStateException 예외가 발생. </td>
  </tr>
  <tr>
    <td><center> offerFirst(Object o) </center></td>
    <td><center> boolean </center></td>
    <td> 요소를 헤드에 추가. </td>
  </tr>
  <tr>
    <td><center> offerLast(Object o) </center></td>
    <td><center> boolean </center></td>
    <td> 요소를 꼬리에 추가. </td>
  </tr>
  <tr>
    <td><center> peekFirst(Object o) </center></td>
    <td><center> E </center></td>
    <td> 헤드에 있는 요소를 삭제하지 않고 반환. </td>
  </tr>
  <tr>
    <td><center> peekLast(Object o) </center></td>
    <td><center> E </center></td>
    <td> 꼬리에 있는 요소를 삭제하지 않고 반환. </td>
  </tr>
  <tr>
    <td><center> pollFirst(Object o) </center></td>
    <td><center> E </center></td>
    <td> 헤드에 있는 요소를 검색 및 삭제하면서 반환. </td>
  </tr>
  <tr>
    <td><center> pollLast(Object o) </center></td>
    <td><center> E </center></td>
    <td> 꼬리에 있는 요소를 검색 및 삭제하면서 반환. </td>
  </tr>
  <tr>
    <td><center> size() </center></td>
    <td><center> int </center></td>
    <td> 요소의 개수를 반환. </td>
  </tr>
</table>
<br/>

목록은 많아 보이지만 Deque가 양방향이기 때문에 헤드와 꼬리를 나누어 메소드가 더 생성 되었을 뿐이다.

&nbsp;&nbsp;&nbsp;이제 각 클래스들을 살펴보자.

&nbsp;&nbsp;&nbsp;낯익은 녀석이 하나 보인다. 그렇다. 바로 LinkedList다. 그림을 보면 알겠지만 LinkedList는 List(리스트)를 구현하기도 하지만, Deque(덱)도 구현한다. 그리고 Deque Interface는 Queue Interface를 상속받는다. 즉, LinkedList는 사실상 3가지 용도로 쓸 수 있다는 것이다. 

실제 LinkedList class를 보면 다음과 같이 List와 Deque를 모두 구현한다.

```java
public class LinkedList<E> extends AbstractSequentialList<E> implements List<E>, Deque<E>, Cloneable, java.io.Serializable {
  transient int size = 0;

  /**
   * Pointer to first node. 
   */
  transient Node<E> first;

  /**
   * Pointer to last node. 
   */
  transient Node<E> last;

  ...
}
```

왜 LinkedList를 받을까? 앞서 List를 설명할 때도 말했지만 ArrayList와 LinkedList의 차이점은 Object[] 배열로 관리하느냐, Node라는 객체를 연결하여 관리하느냐의 차이였다.  
마찬가지로 Deque 또는 Queue를 LinkedList처럼 Node 객체로 연결해서 관리하길 원한다면 LinkedList를 쓰면 된다. 원리 자체가 크게 다르지 않기 때문에 LinkedList 하나에 다중 인터페이스를 포함하고 있는 것이다.

&nbsp;&nbsp;&nbsp;ArrayDeque는 반대로 ArrayList처럼 Object[] 배열로 구현되어 있다. 물론 LinkedList와 ArrayDeque 둘 다 Deque을 구현하고 있고, Deque은 Queue를 상속받기 때문에 Queue로도 쓰일 수 있다.

만약 자바에서 지원하는 컬렉션에서 '일반적인 큐'를 사용하고자 한다면 LinkedList로 생성하여 Queue로 선언하면 된다.

```java
Queue<T> queue = new LinkedList<>();
```

이는 Deque도 마찬가지다.

```java
Deque<T> queue = new LinkedList<>();
```

&nbsp;&nbsp;&nbsp;PriorityQueue는 뭘까? 말 그대로 '우선순위 큐'다.  
LinkedList는 Queue로 사용할 수 있다고 했지만 큐의 원리가 선입선출이라는 전제 아래에 짜여있다. PriorityQueue는 '데이터 우선순위'에 기반하여 우선순위가 높은 데이터가 먼저 나오는 원리다. 따로 정렬방식을 지정하지 않는다면 낮은 숫자가 높은 우선순위를 갖는다. 정렬메소드인 sort()와 같은 순서로 데이터 우선순위를 갖는다는 의미다.  
PriorityQueue는 주어진 데이터들 중 최댓값, 혹은 최솟값을 꺼내올 때 매우 유용하게 사용될 수 있다. 단, 사용자가 정의한 객체를 타입으로 쓸 경우 반드시 Comparator 또는 Comparable을 통해 정렬 방식을 구현해주어야 한다.

```java
ArrayDeque<T> arraydeque = new ArrayDeque<>();
PriorityQueue<T> priorityqueue = new PriorityQueue<>();

Deque<T> arraydeque = new ArrayDeque<>();
Deque<T> linkedlistdeque = new LinkedList<>();

Queue<T> arraydeque = new ArrayDeque<>();
Queue<T> linkedlistdeque = new LinkedList<>();
Queue<T> priorityqueue = new PriorityQueue<>();
```

# Set 셋 / 세트

&nbsp;&nbsp;&nbsp;Set(세트)는 말 그대로 '집합'이다. Set의 가장 큰 특징 두 가지가 있다. 첫 번째는 '데이터를 중복해서 저장할 수 없음'이고 두 번째는 '입력 순서대로의 저장 순서를 보장하지 않는다'이다.

&#128161;단, LinkedHashSet은 Set임에도 불구하고 입력 순서대로의 저장순서를 보장하고 있다. 그러나 데이터를 중복해서 저장할 수 없는 것은 같다.

&nbsp;&nbsp;&nbsp;앞서 설명한 내용을 보면 List계열은 index(Node)로 관리하기 때문에 `add()`같은 메소드를 쓰면 순서대로 저장되었고, Queue 계열 또한 우선순위 큐(PriorityQueue)를 제외하고는 기본적으로 입력한 순서대로 객체가 연결되었다.  
하지만 Set의 경우는 입력받은 순서와 상관없이 데이터를 집합시키기 때문에 입력받은 순서를 보장할 수 없다. 이런 순서 보장이 안된다는 불편함을 개선시키기 위해 LinkedHashSet이 있다. 데이터 중복을 허용하고 싶지 않은데 입력 순서는 보장받고 싶다면 LinkedHashSet을 사용하면 된다.  
그리고 Queue와 유사하게 Set을 상속받고 있는 SortedSet Interface도 있다.

## Set / SortedSet Interface를 구현하는 클래스

1. HashSet
2. LinkedHashSet
3. TreeSet

## Set / SortedSet Interface에 선언된 대표적인 메소드

<table>
  <tr>
    <td><center> 메소드 </center></td>
    <td><center> 리턴 타입 </center></td>
    <td><center> 설 명 </center></td>
  </tr>
  <tr>
    <td><center> add(E e) </center></td>
    <td><center> boolean </center></td>
    <td> 지정된 요소가 없을 경우 추가.<br/>지정된 요소가 존재하는 경우 false를 반환. </td>
  </tr>
  <tr>
    <td><center> contains(Object o) </center></td>
    <td><center> boolean </center></td>
    <td> 지정된 요소가 Set에 존재하는지 확인. </td>
  </tr>
  <tr>
    <td><center> equals(Object o) </center></td>
    <td><center> boolean </center></td>
    <td> 지정된 객체와 현재 집합이 같은지 비교. </td>
  </tr>
  <tr>
    <td><center> isEmpty() </center></td>
    <td><center> boolean </center></td>
    <td> 집합이 비어있으면 true, 아니면 false를 반환. </td>
  </tr>
  <tr>
    <td><center> remove(Object o) </center></td>
    <td><center> boolean </center></td>
    <td> 지정된 객체가 집합에 존재하는 경우 해당 요소를 제거. </td>
  </tr>
  <tr>
    <td><center> size() </center></td>
    <td><center> int </center></td>
    <td> 집합에 있는 요소의 개수를 반환. </td>
  </tr>
  <tr>
    <td><center> clear() </center></td>
    <td><center> void </center></td>
    <td> 집합에 있는 모든 요소를 제거. </td>
  </tr>
  <tr>
    <td><center> first(E e) </center></td>
    <td><center> E </center></td>
    <td> 첫번째 요소(가장 낮은 요소)를 반환. </td>
  </tr>
  <tr>
    <td><center> Last() </center></td>
    <td><center> E </center></td>
    <td> 마지막 요소(가장 높은 요소)를 반환. </td>
  </tr>
  <tr>
    <td><center> headSet(E toElement) </center></td>
    <td><center> SortedSet<\E> </center></td>
    <td> 지정된 요소(toElement)보다 작은 요소들을 집합으로 반환. </td>
  </tr>
  <tr>
    <td><center> tailSet(E fromElement) </center></td>
    <td><center> SortedSet<\E> </center></td>
    <td> 지정된 요소(fromElement)를 포함하여 큰 요소들을 집합으로 반환. </td>
  </tr>
  <tr>
    <td><center> subSet(E from, E to) </center></td>
    <td><center> SortedSet<\E> </center></td>
    <td> 지정된 from요소를 포함하여 from요소보다 크고, 지정된 to요소보다 작은 요소들을 집합으로 반환. </td>
  </tr>
</table>
<br/>

&nbsp;&nbsp;&nbsp;Set Interface을 구현하는 클래스들은 HashSet, LinkedHashSet, TreeSet 3가지가 있다. 좀 더 구체적으로 말하자면 TreeSet은 Set Interface를 상속받은 SortedSet Interface를 구현하고 있다. 그리고 Set의 가장 큰 특징은 '중복되는 데이터를 넣지 못한다'이고, LinkedHashSet을 제외하고 대부분의 Set은 입력 순서대로의 저장순서를 보장하지 않는다.

그럼 이들의 각 특징에 대해 간략하게 알아보고 가자.

&nbsp;&nbsp;&nbsp;먼저 HashSet은 가장 기본적인 Set 컬렉션의 클래스로 입력 순서를 보장하지 않고, 순서도 마찬가지로 보장되지 않는다.  
쉽게 이해할 만한 예시로 게임에서 '닉네임'을 만들거나 아이디를 생성할 때 '중복확인'을 눌러 중복된 닉네임 또는 아이디인지 확인한다. 이는 데이터가 정렬되어 있을 필요도 없고, 빠르게 중복되는 값인지만 찾으면 되기 때문에 유용한 방법이 될 수 있다.  
좀 더 상세하게 말하자면 hash에 의해 데이터의 위치를 특정시켜 해당 데이터를 빠르게 색인(search)할 수 있게 만든 것이다. 즉, Hash기능과 Set컬렉션이 합쳐진 것이 HashSet이다. 그렇기 때문에 삽입, 삭제, 색인이 매우 빠른 컬렉션 중 하나다.

&nbsp;&nbsp;&nbsp;LinkedHashSet의 경우 이름에서 볼 수 있듯이 Link + Hash + Set이 결합된 형태다. LinkedList에서 보면 `add()`메소드를 통해 요소들을 넣은 순서대로 연결한다. 즉, LinkedList의 첫번째 요소부터 차례대로 출력하면 입력했던 순서대로 출력된다는 것이고 이는 순서를 보장한다는 의미다.  
Set의 경우 기본적으로 입력 순서대로의 저장순서를 보장하지 않아 '중복은 허용하지 않으면서 순서를 보장받고 싶은경우'에는 불편할 수 밖에 없다. 이를 보완하기 위해 존재하는 것이 바로 LinkedHashSet인 것이다.  
실생활에서 예로 들자면 페이지를 열 때 만약 해당 페이지가 중복되는 경우에는 캐시를 다시 적재할 필요없지만, 새로운 페이지를 할당해야 할 떄 최근에 사용되지 않은 캐시를 비우려면 가장 오래된 캐시를 비우는 것이 좋다. 이를 LRU 알고리즘(Least Recently Used Algorithm)이라고 하는데, 이 때 입력된(저장된) 순서를 알아야 오래된 캐시를 비울 수 있다. 이에 적용할 수 있는 자료구조 중 하나다.  
단지 이는 이해를 돕기 위한 예시로 실제로는 LRU기법으로 `LinkedHashMap`이라는 자료구조가 대부분을 차지하고 있어 많이 쓰이진 않는다.

&nbsp;&nbsp;&nbsp;TreeSet은 HashSet과 마찬가지로 입력 순서대로의 저장 순서를 보장하지 않으며 중복 데이터 또한 넣지 못한다. 다만 특별한 점이 있다면 SortedSet Interface의 이름에서 알 수 있듯 이를 구현한 TreeSet은 데이터의 '가중치에 따른 순서'대로 정렬되어 보장한다는 것이다.  
앞서 PriorityQueue를 생각해보자. 데이터들이 입력한 순서대로가 아닌 값에 따라 정렬되어 Queue에 담아진다. 마찬가지로 TreeSet은 '중복되지 않으면서 특정 규칙에 의해 정렬된 형태의 집합을 쓰고 싶을 때 쓴다. 정렬된 형태로 있다보니 특정 구간의 집합요소들을 탐색할 때 매우 유용하다.

&#128073;Tree 라는 자료구조 자체가 데이터를 일정 순서에 의해 정렬하는 구조다. 거기에 더해진 것이 바로 Set인 중복값 방지 자료구조인 것이다.

각 클래스별 생성방법은 다음과 같다.

```java
HashSet<T> hashset = new HashSet<>();
LinkedHashSet<T> linkedhashset = new LinkedHashSet<>();
TreeSet<T> treeset = new TreeSet<>();

SortedSet<T> treeset = new TreeSet<>();

Set<T> hashset = new HashSet<>();
Set<T> linkedhashset = new LinkedHashSet<>();
Set<T> treeset = new TreeSet<>();
```

# 적절한 자료구조 사용하기

자바에서 지원하는 대표적인 컬렉션 11가지를 알아보았다. 설명한 컬렉션은 다음과 같다.

- ArrayList
- LinkedList
- Vector
- Stack
- Queue(by LinkedList)
- PriorityQueue
- Deque(by LinkedList)
- ArrayDeque
- HashSet
- LinkedHashSet
- TreeSet

이제 어떤 상황에 어떤 자료구조를 쓰는 것이 알맞은가에 대해 알아보자. 때마침 잘 설명된 이미지가 있다.

<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbIvfpY%2FbtqI7ysSAlg%2Fopwhvycjl6rAYfClVZNeOk%2Fimg.png" width="60%" height="auto" align="center"><br/>

위 내용 중에서 Map에 관해 설명하지 않았는데, Map은 key와 value가 쌍으로 이루어진 자료구조이다. 지금까지 설명한 Collection들은 모두 단일 value이기 때문에 그 쪽 내용은 살짝 넣어두자.

---
**Reference**

- [공식 문서](https://docs.oracle.com/javase/8/docs/api/)
