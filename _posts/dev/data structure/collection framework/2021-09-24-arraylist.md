---
layout: post
date: 2021-09-24 12:57:00
title: "ArrayList"
description: "자료구조"
subject: dev
category: [ cs ]
tags: [ cs, data structure, collection framework ]
use_math: true
comments: true
---

# ArrayList

&nbsp;&nbsp;&nbsp;가장 기본적인 자료구조인 ArrayList를 구현해보자. 실제 Java에서 제공하고 있는 ArrayList는 매우 복잡한 구조라 그대로 따라하기에는 불필요한 부분이 많다. 최소한의 필수 메소드만 구현하고 전체적인 구조를 살펴보는 방향으로 가겠다.  
앞서 List라는 인터페이스를 만들었고 이를 implements하여 구현할 예정이다.

&nbsp;&nbsp;&nbsp;실제로 구현하기 전에 잠깐 알아두고 가야할 점이 있다. ArrayList는 다른 자료구조와 달리 Object[] 배열(객체 배열)을 두고 사용한다.  
또한 모든 자료구조는 '동적 할당'을 전제로 한다. 가끔 ArrayList를 구현 할 때, 리스트가 꽉 차면 리스트의 크기를 늘리지 않고 그냥 꽉 찼다고 더이상 원소를 안 받도록 구현한 경우가 있는데 이렇게하면 자료구조를 구현하는 의미가 없다. 동적 할당을 안하고 사이즈를 정해놓고 구현한다면 메인함수에서 정적배열을 선언하는 것과 차이가 없다. 데이터의 개수를 알 수 없는데 배열을 쓰고 싶을 때 어떤 방법을 선택해야할까? ArrayList, LinkedList등의 자료구조를 선택할 것인데 왜냐면 사이즈를 정하지 않고 동적으로 활용할 수 있기 때문이다.  
마지막으로 리스트 계열 자료구조는 데이터 사이에 빈 공간을 허락하지 않는다.

&nbsp;&nbsp;&nbsp;예시를 통해서 좀 더 알아보자.

```
Object[] a = {"a", "b", "c", "d"}; 
```

이러한 배열이 있고, 만약 "c"라는 데이터를 삭제하려고 한다. (a[2] = null) 그러면 a배열은 다음과 같은 상황일 것이다.

```
Object[] a = {"a", "b", null, "d"};
```

이렇게 데이터 사이에 빈 공간이 생길 경우 빈 공간을 없애야 한다. 즉, null 뒤에 있는 모든 데이터를 한 칸씩 끌어와야한다.

```
Object[] a = {"a", "b", "d", null};
```

이런 식으로 항상 리스트 계열 자료구조는 데이터들이 '연속되어'있어야 한다는 점을 기억하자.

# ArrayList 구현

### 필수 목록

- 클래스 및 생성자 구성
- resize 메소드 구현
- add 메소드 구현
- get, set, indexOf, contains 메소드 구현
- remove 메소드 구현
- size, isEmpty, clear 메소드 구현

### 부가 목록

- clone, toArray 구현

## ArrayList 클래스 및 생성자 구성하기

&nbsp;&nbsp;&nbsp;ArrayList 이름으로 생성해준 뒤 앞서 작성했던 List 인터페이스를 implements해준다. implements를 하면 class 옆에 경고표시가 뜨는데 List 인터페이스에 있는 메소드들을 구현하라는 것이다. 어차피 모두 구현해나갈 것이기 때문에 일단은 무시하자.

```java
import Interface_form.List;

public class ArrayList<E> implements List<E> {

	private static final int DEFAULT_CAPACITY = 10;	// 최소(기본) 크기 
	private static final Object[] EMPTY_ARRAY = {};	// 빈 배열

	private int size;	// 요소 개수 

	Object[] array;	// 요소를 담을 배열 

	// 생성자1 (초기 공간 할당 X)
	public ArrayList() {
		this.array = EMPTY_ARRAY;
		this.size = 0;
	}

	// 생성자2 (초기 공간 할당 O)
	public ArrayList(int capacity) {
		this.array = new Object[capacity];
		this.size = 0;
	}

}
```

&nbsp;&nbsp;&nbsp;하나씩 살펴보자.

- DEFAULT_CAPACITY : 배열이 생성 될 때의 최초 할당 크기(용적)이자 최소 할당 용적 변수로 기본값을 10으로 설정했다.
- EMPTY_ARRAY : 아무 것도 없는 빈 배열
- size : 배열에 담긴 요소(원소)의 개수
- array : 요소를 담을 배열

그리고 DEFAULT_CAPACITY 변수와 EMPTY_ARRAY 변수는 상수로 쓸 것이기 때문에 static final 키워드를 붙인다.

&nbsp;&nbsp;&nbsp;생성자를 2개만 들었는데 각각을 살펴보자.  
생성자 1의 경우 공간 할당 없이 객체만 생성하고 싶을 때, 즉 다음과 같은 상태일 때이다.

```
ArrayList<Integer> list = new ArrayList<>();
```

이 때는 사용자가 공간 할당을 명시하지 않았기 때문에 array 변수를 EMPTY_ARRAY로 초기화 시켜 놓는다.

&nbsp;&nbsp;&nbsp;생성자 2는 사용자가 데이터의 개수를 예측할 수 있어서 미리 공간 할당을 해놓고 싶을 경우, 즉 다음과 같이 생성할 경우

```
ArrayList<Integer> list = new ArrayList<>(100);
```

array의 공간 할당을 입력 된 수 만큼(array = new Object[100]) 배열을 만든다. 그리고 마찬가지로 size를 0으로 초기화 시킨다.
&#128073;size는 요소(원소)의 개수를 의미한다. 공간 할당과는 다른 개념이다.

## 동적할당을 위한 resize 메소드 구현

&nbsp;&nbsp;&nbsp;들어오는 데이터에 개수에 따라 '최적화'된 용적을 갖을 필요가 있다. 만약 데이터는 적은데 용적이 크면 메모리가 낭비되고, 반대로 용적은 적은데 데이터가 많으면 넘치는 데이터들은 보관할 수 없게 되는 상황을 마주칠 수 있다.  
그렇기 때문에 size(요소의 개수)가 용적(capacity)에 얼마만큼 차있는지를 확인하고, 적절한 크기에 맞게 배열의 용적을 변경해야 한다. 또한 용적은 외부에서 마음대로 접근하면 데이터의 손상을 야기할 수 있기 때문에 private로 접근을 제한해주도록 하자.

```java
private void resize() {

	int array_capacity = array.length;

	// if array's capacity is 0
	if (Arrays.equals(array, EMPTY_ARRAY)) {
		array = new Object[DEFAULT_CAPACITY];
		return;
	}

	// full
	if (size == array_capacity) {
		int new_capacity = array_capacity * 2;

		// copy
		array = Arrays.copyOf(array, new_capacity);
		return;
	}
	// half full
	if (size < (array_capacity / 2)) {
		int new_capacity = array_capacity / 2;

		// copy
		array = Arrays.copyOf(array, Math.max(new_capacity, DEFAULT_CAPACITY));
		return;
	}

}
```

현재 array의 용적(=array 길이)과 데이터의 개수(size)를 비교한다. 

### 조건문 1 : if (Arrays.equals(array, EMPTY_ARRAY)) 

&nbsp;&nbsp;&nbsp;생성자에서 사용자가 용적을 별도로 설정하지 않은 경우 EMPTY_ARRAY로 초기화 되어있어 용적이 0인 상태다. 이 경우를 고려하여 이제 새로 array의 용적을 할당하기 위해 최소 용적으로 설정해두었던 DEFAULT_CAPACITY의 크기만큼 배열을 생성해주고 메소드를 종료한다.  

또한 주소가 아닌 값을 비교해야 하기 때문에 Arrays.equals() 메소드를 사용하도록 하자.

### 조건문 2 :  if (size == array_capacity) 

&nbsp;&nbsp;&nbsp;데이터가 꽉 찰 경우에는 데이터(요소)를 더 받아오기 위해서 용적을 늘려야 한다. 즉, 데이터의 개수가 용적과 같을 경우는 꽉 차있는 경우를 말한다. 이 때는 새롭게 용적을 늘릴 필요가 있으므로 새로운 용적을 현재 용적의 2배로 설정하도록 한다. 

또한 기존에 담겨있던 요소들을 새롭게 복사해와야한다. 이 때 편리하게 쓸 수 있는 것이 `Arrays.copyOf()` 메소드다. Arrays.copyOf()는 첫 번째 파라미터로 '복사할 배열'을 넣어주고, 두 번째 파라미터는 용적의 크기를 넣어주면 된다.

만약 복사할 배열보다 용적의 크기가 클 경우 먼저 배열을 복사한 뒤, 나머지 빈 공간은 null로 채워지기 때문에 편리하다.

### 조건문 3 :  if (size < (array_capacity / 2 )) 

데이터가 용적에 절반 미만으로 차지 않을 경우다. 이 경우 데이터는 적은데 빈 공간이 메모리를 차지하고 있어 불필요한 공간을 낭비할 뿐이다. 이럴 때에는 사이즈를 적절하게 줄여주는 것이 좋다.

데이터의 개수가 용적의 절반 미만이라면 용적도 절반으로 줄여주도록 하기 위해 새로운 용적(new_capacity)을 현재 용적의 절반으로 둔 뒤, Arrays.coptyOf() 메소드를 통해 새로운 용적의 배열을 생성해주도록 하자.

만약 복사할 배열보다 용적의 크기가 작을경우 새로운 용적까지만 복사하고 반환하기 때문에 예외발생에 대해 안전하다.

## add 메소드 구현

&nbsp;&nbsp;&nbsp;ArrayList에 데이터를 추가할 수 있도록 리스트 인터페이스에 있는 add() 메소드를 구현할 것이다.

add 메소드에는 크게 3가지로 분류가 된다.

- 가장 마지막 부분에 추가 (기본값) - addLast(E value)

- 가장 앞부분에 추가 - addFirst(E value)

- 특정 위치에 추가 - add(int index, E value) 

현재 자바에 내장되어있는 ArrayList에서는 addLast()역할을 add() 메소드가 하고, 특정 위치에 추가는 add(int index, E element)로 오버로딩된 메소드가 하며, addFisrt()라는 메소드는 없다. 하지만 좀 더 쉽게 보기 위해서 나누어서 만들 것이다.

### 1. 기본삽입 : add(E value) & addLast(E value) 메소드

&nbsp;&nbsp;&nbsp;먼저 add()의 기본 값은 addLast()이다. 가장 마지막 부분에만 추가하면 되기 때문에 그리 어렵지 않다.

addLast() 메소드를 구현하면 아래와 같다. 자바 내부에서 add메소드는 boolean을 리턴하기 때문에 boolean 메소드로 구현해주었다.

```java
@Override
public boolean add(E value) {
	addLast(value);
	return true;
}
 
public void addLast(E value) {

	// 가득차있는 상태라면 용적 재할당
	if (size == array.length) {
		resize();
	}

	array[size] = value;	// 마지막 위치에 요소 추가
	size++;	// 사이즈 1 증가
}
```

&nbsp;&nbsp;&nbsp;addLast 메소드를 보자. add를 호출하면 파라미터로 넘어온 value는 addLast로 보내진다. 그리고 데이터를 넣기에 앞서 현재 용적이 가득차있는지 검사한다.  
만약 가득차있다면 우리가 만들었던 resize() 메소드를 호출해서 array가 더 큰 용적을 갖도록 만들어 준다. 그리고 마지막 위치(size)에 value를 추가해주고 size를 1 증가시켜준다.

size가 요소의 개수이고, index는 0부터 시작하기 때문에 `array[size] = value;` 를 해주면 된다. 이 부분을 헷갈려서 잘못된 인덱스에 참조하는 경우가 많으니 주의할 필요가 있다.

### 2. 중간삽입 : add(int index, E value) 

&nbsp;&nbsp;&nbsp;중간에 추가하는 것은 우선 index가 범위를 벗어나진 않는지 확인하고, 중간에 삽입할 경우 기존에 있던 index의 요소와 그의 뒤에 있는 데이터들을 모두 한 칸씩 뒤로 밀어야한다.  
예를 들어 add에서 index로 들어오는 파라미터에 대해, 만약 값이 3이 들어왔다면 원래 있던 배열에서 3을 포함한 뒤에 있는 모든 요소들을 한 칸씩 뒤로 옮긴 뒤에 3의 위치에 새로운 데이터를 삽입 해주는 방식이다.

addLast가 단순히 데이터를 추가하는 과정이였다면 중간 삽입은 데이터를 미루는 코드가 추가된 것이다.

```java
@Override
public void add(int index, E value) {
 
	if (index > size || index < 0) {	// 영역을 벗어날 경우 예외 발생 
		throw new IndexOutOfBoundsException();
	}

	if (index == size) {	// index가 마지막 위치라면 addLast 메소드로 요소추가
		addLast(value);
	} else {
		
		if(size == array.length) {	// 가득차있다면 용적 재할당
			resize();
		}
		
		// index 기준 후자에 있는 모든 요소들 한 칸씩 뒤로 밀기
		for (int i = size; i > index; i--) {
			array[i] = array[i - 1];
		}
 
		array[index] = value;	// index 위치에 요소 할당
		size++;
	}

}
```

먼저 index로 들어오는 값이 size를 벗어나는지(빈 공간을 허용하지 않으므로), 또는 음수가 들어오는지를 확인한다. 만약에 범위를 벗어나거나 인덱스가 음수가 들어오면 예외를 발생시키도록 한다. 그리고 사용자가 넘겨준 index가 size와 같다는 것은 결국 가장 마지막에 추가하는 것과 같은 의미이므로 이미 만들어두었던 addLast() 메소드로 가도록 한다.  
그 외의 경우가 이제 중간에 삽입되는 경우다. 당연하게도 size가 현재 배열의 용적과 같다는 것은 이미 가득차서 더이상 들어올 공간이 없다는 뜻이므로 resize()메소드를 호출해줌으로써 용적량을 늘리도록 한다. 그리고 index와 그 후방에 있는 데이터들을 한 칸씩 뒤로 밀어야 하므로 반복문을 통해 뒤로 밀어주도록 한 뒤 array[index]에는 새로운 요소로 교체해주도록 한다.

### 3. addFirst(E value)

&nbsp;&nbsp;&nbsp;addFirst는 간단하다. 기존 데이터가 있다면 어차피 모든 데이터들을 뒤로 밀어야하기 때문에 앞서 만들었던 중간 삽입에서 index를 0으로 보내면 된다.

```java
public void addFirst(E value) {
	add(0, value);
}
```

&nbsp;&nbsp;&nbsp;이렇게 기본적인 요소 추가 메소드를 만들었다. 만든 메소드를 한 번에 보면 아래와 같다. 

```java
@Override
public boolean add(E value) {
	addLast(value);
	return true;
}

public void addLast(E value) {
	if (size == array.length) {
		resize();
	}

	array[size] = value;
	size++;
}

@Override
public void add(int index, E value) {

	if (index > size || index < 0) {
		throw new IndexOutOfBoundsException();
	}

	if (index == size) {
		addLast(value);
	} else {
		
		if(size == array.length) {
			resize();
		}
		
		for (int i = size; i > index; i--) {
			array[i] = array[i - 1];
		}

		array[index] = value;
		size++;
	}

}

public void addFirst(E value) {
	add(0, value);
}
```

---
**Reference**

- [공식 문서](https://docs.oracle.com/javase/8/docs/api/)