---
layout: post
date: 2021-09-20 22:26:00
title: "List Interface(리스트 인터페이스)"
description: "자료구조"
subject: dev
category: [ cs ]
tags: [ cs, data structure, collection framework ]
use_math: true
comments: true
---

# List interface

&nbsp;&nbsp;&nbsp;대부분 자료구조를 학습할 때 가장 먼저 배우는 것은 List 계열일 것이다. 우리는 이러한 것과 비슷한 것을 이미 사용하고 있었는데 바로 배열이다.  
자바 컬렉션 프레임워크에서 지원하는 ArrayList, LinkedLIst 같은 리스트 클래스들은 데이터들을 좀 더 다루기 쉽게 메소드로 구현한 것이다.

&nbsp;&nbsp;&nbsp;배열과 List 인터페이스에서 지원하는 클래스들(ex. ArrayList, LinkedList...)의 공통점과 차이점은 무엇인지 간단하게 알아보자.

### 공통점

1. 동일한 특성의 데이터들을 묶는다.
2. 반복문(Loop) 내에 변수를 이용하여 하나의 묶음 데이터를 모두 접근할 수 있다.

### 차이점 - 배열

1. 처음 선언한 배열의 크기(길이)는 변경할 수 없다. 이러한 것을 정적 할당(static allocation)이라고 한다.
2. 메모리에 연속적으로 나열되어 할당된다.
3. index에 위치한 하나의 데이터(element)를 삭제하면 해당 index는 빈공간으로 계속 존재한다.

### 차이점 - 리스트

1. 리스트의 길이는 가변적이다. 이를 동적 할당(dynamic allocation)이라고 한다.
2. 데이터들이 연속적으로 나열된다. 메모리에 연속적으로 나열되지 않고 각 데이터들은 주소(reference)로 연결되어있다.
3. 데이터(element) 사이에 빈 공간을 허용하지 않는다.

이러한 특징들로 인해 장단점도 명확하게 알 수 있다.

### 배열의 장단점

<장점>

1. 데이터 크기가 정해져있을 경우 메모리 관리가 편하다.
2. 메모리에 연속적으로 나열되어 할당하기 때문에 index를 통한 색인(접근)속도가 빠르다.

<단점>

1. 배열의 크기를 변경할 수 없기 때문에 초기에 너무 큰 크기로 설정하면 메모리 낭비가 심해지고, 너무 작은 크기로 설정하면 데이터를 담지 못하는 경우가 발생 할 수 있다.
2. 빈 공간을 허용하지 않고 데이터를 삽입(add), 삭제(remove)를 하려고 하면 뒤의 데이터들을 모두 밀어내거나 당겨주어야 한다. 따라서 속도가 느리기 때문에 삽입, 삭제가 빈번한 경우에는 유용하지 않다.

### 리스트의 장단점

<장점>

1. 데이터의 개수에 따라 해당 개수만큼 메모리를 동적 할당해주기 때문에 메모리 관리가 편리해진다.
2. 빈 공간을 허용하지 않기 때문에 데이터 관리에도 편하다.
3. 포인터(주소)로 각 데이터들이 연결되어 있기 때문에 해당 데이터에 연결된 주소만 바꿔주면 되기 때문에 삽입 삭제에 용이하다.(단, ArrayList는 예외)

<단점>

1. 객체로 데이터를 다루기 때문에 적은 양의 데이터만 쓸 경우 배열에 비해 차지하는 메모리가 커진다.
2. 기본적으로 주소를 기반으로 구성되어있고 메모리에 순차적으로 할당하는 것이 아니기 때문에(물리적 주소가 순차적이지 않다) 색인(검색)능력은 떨어진다.

&nbsp;&nbsp;&nbsp;앞선 글에서 봤듯이 List 인터페이스에 구현된 메소드 중 자주 사용하는 메소드들은 다음과 같다.

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

&nbsp;&nbsp;&nbsp;이를 토대로 앞으로 구현 할 인터페이스를 만들어보자.  
&#128073;자료구조를 만들 때, 인터페이스들은 같은 프로젝트 폴더 안에 별도의 package를 하나 만들어서 그 안에 모아놓는 것을 추천한다.

```java
package Interface_form;
 
/**
 * 
 * List Interface
 * List는 ArrayList, SinglyLinkedList, DoublyLinked에 의해 각각 구현.
 * 
 * @author gracenam
 * @param <E> the type of elements in this list
 *
 * @version 1.0
 * 
 */
 
public interface List<E> {
 
	/**
	 * 리스트에 요소를 추가
	 * 
	 * @param value 리스트에 추가할 요소
	 * @return 리스트에서 중복을 허용하지 않을 경우에 리스트에 이미 중복되는 
	 *         요소가 있을 경우 {@code false}를 반환하고, 중복되는 원소가
	 *         없을경우 {@code true}를 반환
	 */
	boolean add(E value);
 
	/**
	 * 리스트에 요소를 특정 위치에 추가. 
	 * 특정 위치 및 이후의 요소들은 한 칸씩 뒤로 밀린다.
	 * 
	 * @param index 리스트에 요소를 추가할 특정 위치 변수
	 * @param value 리스트에 추가할 요소
	 */
	void add(int index, E value);
 
	/**
	 * 리스트의 index 위치에 있는 요소를 삭제.
	 * 
	 * @param index 리스트에서 삭제 할 위치 변수
	 * @return 삭제된 요소를 반환
	 */
	E remove(int index);
 
	/**
	 * 리스트에서 특정 요소를 삭제. 동일한 요소가 
	 * 여러 개일 경우 가장 처음 발견한 요소만 삭제.
	 * 
	 * @param value 리스트에서 삭제할 요소
	 * @return 리스트에 삭제할 요소가 없거나 삭제에 실패할 
	 *         경우 {@code false}를 반환하고 삭제에 성공할 경우 {@code true}를 반환 
	 */
	boolean remove(Object value);
 
	/**
	 * 리스트에 있는 특정 위치의 요소를 반환.
	 * 
	 * @param index 리스트에 접근할 위치 변수 
	 * @return 리스트의 index 위치에 있는 요소 반환 
	 */
	E get(int index);
 
	/**
	 * 리스트에서 특정 위치에 있는 요소를 새 요소로 대체.
	 * 
	 * @param index 리스트에 접근할 위치 변수 
	 * @param value 새로 대체할 요소 변수 
	 */
	void set(int index, E value);
 
	/**
	 * 리스트에 특정 요소가 리스트에 있는지 여부를 확인.
	 * 
	 * @param value 리스트에서 찾을 특정 요소 변수 
	 * @return 리스트에 특정 요소가 존재할 경우 {@code true}, 존재하지 않을 경우 {@code false}를 반환  
	 */
	boolean contains(Object value);
 
	/**
	 * 리스트에 특정 요소가 몇 번째 위치에 있는지를 반환.
	 * 
	 * @param value 리스트에서 위치를 찾을 요소 변수  
	 * @return 리스트에서 처음으로 요소와 일치하는 위치를 반환.
	 *         만약 일치하는 요소가 없을경우 -1 을 반환 
	 */
	int indexOf(Object value);
 
	/**
	 * 리스트에 있는 요소의 개수를 반환.
	 * 
	 * @return 리스트에 있는 요소 개수를 반환  
	 */
	int size();
 
	/**
	 * 리스트에 요소가 비어있는지를 반환.
	 * @return 리스트에 요소가 없을경우 {@code true}, 요소가 있을경우 {@code false}를 반환 
	 */
	boolean isEmpty();
 
	/**
	 * 리스트에 있는 요소를 모두 삭제.
	 */
	public void clear();
 
}
```

자료구조들을 구현할 때 다음과 같은 규칙에 맞춰서 작성할 예정이다.

1. 제네릭(Generic)은 한 개일 경우 E, 두 개일 경우 E와 T로 표현한다. 보통 많이 쓰이는 제네릭 이름은 T, K, V, E가 많기 때문에 대표적인 E와 T를 쓴다.
2. 최대한 Java API 문서에 나와있는 제공되는 메소드들과 유사하게 만든다. 기준은 다음 링크를 참조하자. <https://docs.oracle.com/en/java/javase/11/docs/api/index.html>
3. 재정의(Override), 예외처리(throw), 객체(Object), 인터페이스(nterface), 제네릭(Generic)에 대한 설명은 생략한다.

---
**Reference**

- [공식 문서](https://docs.oracle.com/javase/8/docs/api/)
