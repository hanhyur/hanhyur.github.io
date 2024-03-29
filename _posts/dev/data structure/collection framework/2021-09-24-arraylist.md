---
layout: post
date: 2021-09-24 12:57:00
title: "ArrayList"
description: "자료구조"
subject: dev
category: [cs]
tags: [cs, data structure, collection framework]
use_math: true
comments: true
---

# ArrayList

&nbsp;&nbsp;&nbsp;가장 기본적인 자료구조인 ArrayList를 구현해보자. 실제 Java에서 제공하고 있는 ArrayList는 매우 복잡한 구조라 그대로 따라하기에는 불필요한 부분이 많다. 최소한의 필수 메서드만 구현하고 전체적인 구조를 살펴보는 방향으로 가겠다.  
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
- resize 메서드 구현
- add 메서드 구현
- get, set, indexOf, contains 메서드 구현
- remove 메서드 구현
- size, isEmpty, clear 메서드 구현

### 부가 목록

- clone, toArray 구현

## ArrayList 클래스 및 생성자 구성하기

&nbsp;&nbsp;&nbsp;ArrayList 이름으로 생성해준 뒤 앞서 작성했던 List 인터페이스를 implements해준다. implements를 하면 class 옆에 경고표시가 뜨는데 List 인터페이스에 있는 메서드들을 구현하라는 것이다. 어차피 모두 구현해나갈 것이기 때문에 일단은 무시하자.

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

## 동적할당을 위한 resize 메서드 구현

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

&nbsp;&nbsp;&nbsp;생성자에서 사용자가 용적을 별도로 설정하지 않은 경우 EMPTY_ARRAY로 초기화 되어있어 용적이 0인 상태다. 이 경우를 고려하여 이제 새로 array의 용적을 할당하기 위해 최소 용적으로 설정해두었던 DEFAULT_CAPACITY의 크기만큼 배열을 생성해주고 메서드를 종료한다.

또한 주소가 아닌 값을 비교해야 하기 때문에 Arrays.equals() 메서드를 사용하도록 하자.

### 조건문 2 : if (size == array_capacity)

&nbsp;&nbsp;&nbsp;데이터가 꽉 찰 경우에는 데이터(요소)를 더 받아오기 위해서 용적을 늘려야 한다. 즉, 데이터의 개수가 용적과 같을 경우는 꽉 차있는 경우를 말한다. 이 때는 새롭게 용적을 늘릴 필요가 있으므로 새로운 용적을 현재 용적의 2배로 설정하도록 한다.

또한 기존에 담겨있던 요소들을 새롭게 복사해와야한다. 이 때 편리하게 쓸 수 있는 것이 `Arrays.copyOf()` 메서드다. Arrays.copyOf()는 첫 번째 파라미터로 '복사할 배열'을 넣어주고, 두 번째 파라미터는 용적의 크기를 넣어주면 된다.

만약 복사할 배열보다 용적의 크기가 클 경우 먼저 배열을 복사한 뒤, 나머지 빈 공간은 null로 채워지기 때문에 편리하다.

### 조건문 3 : if (size < (array_capacity / 2 ))

데이터가 용적에 절반 미만으로 차지 않을 경우다. 이 경우 데이터는 적은데 빈 공간이 메모리를 차지하고 있어 불필요한 공간을 낭비할 뿐이다. 이럴 때에는 사이즈를 적절하게 줄여주는 것이 좋다.

데이터의 개수가 용적의 절반 미만이라면 용적도 절반으로 줄여주도록 하기 위해 새로운 용적(new_capacity)을 현재 용적의 절반으로 둔 뒤, Arrays.coptyOf() 메서드를 통해 새로운 용적의 배열을 생성해주도록 하자.

만약 복사할 배열보다 용적의 크기가 작을경우 새로운 용적까지만 복사하고 반환하기 때문에 예외발생에 대해 안전하다.

## add 메서드 구현

&nbsp;&nbsp;&nbsp;ArrayList에 데이터를 추가할 수 있도록 리스트 인터페이스에 있는 add() 메서드를 구현할 것이다.

add 메서드에는 크게 3가지로 분류가 된다.

- 가장 마지막 부분에 추가 (기본값) - addLast(E value)

- 가장 앞부분에 추가 - addFirst(E value)

- 특정 위치에 추가 - add(int index, E value)

현재 자바에 내장되어있는 ArrayList에서는 addLast()역할을 add() 메서드가 하고, 특정 위치에 추가는 add(int index, E element)로 오버로딩된 메서드가 하며, addFisrt()라는 메서드는 없다. 하지만 좀 더 쉽게 보기 위해서 나누어서 만들 것이다.

### 1. 기본삽입 : add(E value) & addLast(E value) 메서드

&nbsp;&nbsp;&nbsp;먼저 add()의 기본 값은 addLast()이다. 가장 마지막 부분에만 추가하면 되기 때문에 그리 어렵지 않다.

addLast() 메서드를 구현하면 아래와 같다. 자바 내부에서 ad 메서드는 boolean을 리턴하기 때문에 boolean 메서드로 구현해주었다.

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

&nbsp;&nbsp;&nbsp;addLast 메서드를 보자. add를 호출하면 파라미터로 넘어온 value는 addLast로 보내진다. 그리고 데이터를 넣기에 앞서 현재 용적이 가득차있는지 검사한다.  
만약 가득차있다면 우리가 만들었던 resize() 메서드를 호출해서 array가 더 큰 용적을 갖도록 만들어 준다. 그리고 마지막 위치(size)에 value를 추가해주고 size를 1 증가시켜준다.

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

	if (index == size) {	// index가 마지막 위치라면 addLast 메서드로 요소추가
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

먼저 index로 들어오는 값이 size를 벗어나는지(빈 공간을 허용하지 않으므로), 또는 음수가 들어오는지를 확인한다. 만약에 범위를 벗어나거나 인덱스가 음수가 들어오면 예외를 발생시키도록 한다. 그리고 사용자가 넘겨준 index가 size와 같다는 것은 결국 가장 마지막에 추가하는 것과 같은 의미이므로 이미 만들어두었던 addLast() 메서드로 가도록 한다.  
그 외의 경우가 이제 중간에 삽입되는 경우다. 당연하게도 size가 현재 배열의 용적과 같다는 것은 이미 가득차서 더이상 들어올 공간이 없다는 뜻이므로 resize( 메서드를 호출해줌으로써 용적량을 늘리도록 한다. 그리고 index와 그 후방에 있는 데이터들을 한 칸씩 뒤로 밀어야 하므로 반복문을 통해 뒤로 밀어주도록 한 뒤 array[index]에는 새로운 요소로 교체해주도록 한다.

### 3. addFirst(E value)

&nbsp;&nbsp;&nbsp;addFirst는 간단하다. 기존 데이터가 있다면 어차피 모든 데이터들을 뒤로 밀어야하기 때문에 앞서 만들었던 중간 삽입에서 index를 0으로 보내면 된다.

```java
public void addFirst(E value) {
	add(0, value);
}
```

&nbsp;&nbsp;&nbsp;이렇게 기본적인 요소 추가 메서드를 만들었다. 만든 메서드를 한 번에 보면 아래와 같다.

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

## get, set, indexOf, contains 메서드 구현

&nbsp;&nbsp;&nbsp;remove 메서드 전에 다른 메서드들을 먼저 구현하는 이유는 remove 메서드에서 유사한 동작을 필요로 하기 때문에 비슷한 메서드들을 먼저 구현 한 뒤 remove 구현파트에서 이를 응용하면 효율적이기 때문이다.

### 1. get(int index) 메서드

&nbsp;&nbsp;&nbsp;get()은 리스트를 써봤다면 쉽게 떠오를 것이다. index로 들어오는 값을 인덱스 삼아 해당 위치에 있는 요소를 반환하는 메서드다. 배열의 위치를 찾아가는 것이기 때문에 반드시 잘못된 위치 참조에 대한 예외처리를 해주어야 한다.

```java
@SuppressWarnings("unchecked")
@Override
public E get(int index) {
	if(index >= size || index < 0) {	// 범위 벗어나면 예외 발생
		throw new IndexOutOfBoundsException();
	}
	// Object 타입에서 E타입으로 캐스팅 후 반환
	return (E) array[index];
}
```

&nbsp;&nbsp;&nbsp;여기서 `@SuppressWarnings("unchecked")`은 뭘까? `@SuppressWarnings("unchecked")`을 붙이지 않으면 type safe(타입 안정성)에 대해 경고를 보낸다. 반환되는 것을 보면 E 타입으로 캐스팅을 하고 있고 그 대상이 되는 것은 Object[] 배열의 Object 데이터다. 즉, Object -> E 타입으로 변환을 하는 것인데 이 과정에서 변환할 수 없는 타입을 가능성이 있다는 경고로 메서드 옆에 경고표시가 뜨는데 add하여 받아들이는 데이터 타입은 유일하게 E 타입만 존재한다.  
그렇기 때문에 형 안정성이 보장된다. 한마디로 `ClassCastException`이 뜨지 않으니 이 경고들을 무시하겠다는 것이다. 형 변환시 예외 가능성이 없을 확실한 경우에 최소한의 범위에서 써주는 것이 좋다. 그렇지 않으면 중요한 경고 메세지를 놓칠 수도 있기 때문이다.

&nbsp;&nbsp;&nbsp;get 메서드에서도 index가 음수이거나, size와 같거나 큰 수가 들어올 경우 잘못된 참조를 하고 있기 때문에 `IndexOutOfBoundsException()` 예외를 발생시킨다. 만약 index가 정상적인 참조가 가능한 값일 경우 해당 인덱스의 요소를 반환해준다. 이 때 원본 데이터 타입으로 반환하기 위해 E 타입으로 캐스팅을 해준다.

### 2. set(int index, E value) 메서드

&nbsp;&nbsp;&nbsp;set 메서드는 기존에 index에 위치한 데이터를 새로운 데이터(value)으로 '교체'하는 것이다. add 메서드가 데이터 '추가'였다면 set 메서드는 '교체'인 것이다. 결과적으로 index에 위치한 데이터를 교체하는 것이기 때문에 get 메서드와 유사하다. 다만 get은 해당 인덱스의 값을 반환하는 것이였다면 set은 데이터만 교체해주면 된다.

```java
@Override
public void set(int index, E value) {
	if (index >= size || index < 0) {	// 범위를 벗어날 경우 예외 발생
		throw new IndexOutOfBoundsException();
	}	else {
		// 해당 위치의 요소를 교체
		array[index] = value;
	}
}
```

마찬가지로 잘못된 인덱스를 참조하는지 반드시 검사가 필요하다. index로 참조하는 모든 메서드들은 반드시 검사해야한다고 생각하면 편하다.

### 3. indexOf(Object value) 메서드

&nbsp;&nbsp;&nbsp;indexOf 메서드는 사용자가 찾고자 하는 요소(value)의 '위치(index)'를 반환하는 메서드다. 이 때 생기는 궁금즘이 있다. 찾고자 하는 요소가 중복된다면 어떻게 반환해야할까? 이에 대한 답은 "가장 먼저 마주치는 요소의 인덱스를 반환한다"이다. 찾고자 하는 요소가 없다면 -1을 반환하면 된다.

한 가지 중요한 점은 객체끼리 비교할 때는 동등연산자(==)가 아니라 반드시 .equals()로 비교해야 한다. 객체끼리 비교할 때 동등연산자를 쓰면 값을 비교하는 것이 아닌 주소를 비교하는 것이기 때문에 잘못된 결과가 나타나게 된다.

```java
@Override
public int indexOf(Object value) {
	int i = 0;

	// value와 같은 객체(요소 값)일 경우 i(위치) 반환
	for (i = 0; i < size; i++) {
		if (array[i].equals(value)) {
			return i;
		}
	}

	// 일치하는 것이 없을경우 -1을 반환
	return -1;
}
```

### 3-1. LastindexOf(Object value) 메서드

&nbsp;&nbsp;&nbsp;indexOf 메서드는 index가 0부터 시작했다면 반대로 거꾸로 탐색하는 과정도 있는 것이 좋을 것 같다. 사용자가 찾고자 하는 인덱스가 뒤쪽이라고 예상 될 때 굳이 앞에서부터 찾을 필요가 없다. 또한 이후에 구현 할 Stack에서도 이용 가능하므로 미리 만들어 두도록 하자.

```java
public int lastIndexOf(Object value) {
	for(int i = size - 1; i >= 0; i--) {
		if(array[i].equals(value)) {
			return i;
		}
	}

	return -1;
}
```

### 4. contains(Object value) 메서드

&nbsp;&nbsp;&nbsp;indexOf 메서드가 사용자가 찾고자 하는 요소(value)의 '위치(index)'를 반환하는 메서드였다면, contains는 사용자가 찾고자 하는 요소(value)가 존재의 유무를 반환하는 메서드다. 찾고자 하는 요소가 존재한다면 true를, 존재하지 않는다면 false를 반환한다.  
그러면 indexOf와 기능이 비슷하니깐 이를 쓸 수 있을 것 같은데? 라는 생각이 들었다면 아주 훌륭하다. 어차피 해당 요소가 존재하는지를 '검사'한다는 기능은 같기 때문에 indexOf 메서드를 이용하여 만약 음수가 아닌 수가 반환되었다면 요소가 존재한다는 뜻이고, 음수(-1)이 나왔다면 요소가 존재하지 않는다는 뜻이다.

```java
@Override
public boolean contains(Object value) {

	// 0 이상이면 요소가 존재한다는 뜻
	if(indexOf(value) >= 0) {
		return true;
	}	else {
		return false;
	}
}
```

이렇게 get, set, indexOf, contains 메서드들을 만들어보았다.

```java
@SuppressWarnings("unchecked")
@Override
public E get(int index) {
	if(index >= size || index < 0) {
		throw new IndexOutOfBoundsException();
	}

	return (E) array[index];
}

@Override
public void set(int index, E value) {
	if (index >= size || index < 0) {
		throw new IndexOutOfBoundsException();
	} else {
		array[index] = value;
	}
}

@Override
public int indexOf(Object value) {
	int i = 0;

	for (i = 0; i < size; i++) {
		if (array[i].equals(value)) {
			return i;
		}
	}

	return -1;
}

public int lastIndexOf(Object value) {
	for(int i = size - 1; i >= 0; i--) {
		if(array[i].equals(value)) {
			return i;
		}
	}

	return -1;
}

@Override
public boolean contains(Object value) {
	if(indexOf(value) >= 0) {
		return true;
	}	else {
		return false;
	}
}
```

## remove 메서드 구현

&nbsp;&nbsp;&nbsp;remove 메서드의 경우 크게 2가지로 나눌 수 있다.

- 특정 index의 요소를 삭제 - remove(int index)
- 특정 요소를 삭제 - remove(Object value)

자바에 내장되어 있는 ArrayList 역시 remove() 메서드가 없다. remove(int index)와 remove(Object value) 메서드만 존재한다. 하지만 이후에 다룰 Stack이나 LinkedList, Queue 등 다양한 자료구조들은 remove() 메서드가 존재하기 때문에 염두해두어야 한다.  
만약 remove() 기능을 넣어 가장 앞 또는 뒤의 요소를 제거하고 싶다면 remove(int index)을 호출하여 파라미터로 0 또는 size-1을 넘겨주면 된다.

### 1. remove(int index) 메서드

&nbsp;&nbsp;&nbsp;remove(int index)는 '특정 위치에 있는 요소를 제거'하는 것이다. 쉽게 생각해서 index에 위치한 데이터를 삭제하고, 해당 위치 이후의 데이터들을 한 칸씩 당겨오는 것이다. add(int index, E value)를 했던 방식을 거꾸로 하면 된다.

index의 요소를 임시 변수에 담고 배열에서 지운 후 데이터들을 한 칸씩 당겨준다. 데이터 사이에 빈 공간을 채웠으면 size 값을 줄여주면 된다. 데이터가 일정량 이상 비워진 경우 용적을 줄이기 위해 resize() 메서드를 가장 마지막에 추가해주자.

```java
@SuppressWarnings("unchecked")
@Override
public E remove(int index) {

	if (index >= size || index < 0) {
		throw new IndexOutOfBoundsException();
	}

	E element = (E) array[index];	// 임시 저장
	array[index] = null;

	// 삭제한 요소의 뒤에 있는 모든 요소들을 한 칸씩 당겨줌
	for (int i = index; i < size; i++) {
		array[i] = array[i + 1];
		array[i + 1] = null;
	}

	size--;
	resize();

	return element;
}
```

삭제된 원소를 반환해야 되기 때문에 Object 타입을 E 타입으로 캐스팅을 해주면서 경고창이 뜬다. get( 메서드에서 설명했듯이 삭제되는 원소 또한 E Type 외에 들어오는 것이 없기 때문에 형 안정성이 확보되므로 경고표시를 무시하기 위해 `@SuppressWarnings("unchecked")`을 붙인다.

&nbsp;&nbsp;&nbsp;그리고 항상 마지막 원소의 인덱스는 size 보다 1 작다. 그렇기 때문에 범위 체크와 이후의 배열 요소들을 한 칸씩 당겨줄 때 시작 점과 끝 점을 잘 생각하면서 참조해야한다.  
명시적으로 요소를 null로 처리해주어야 GC에 의해 더이상 쓰지 않는 데이터의 메모리를 수거(반환)해주기 때문에 최대한 null 처리를 하는 것이 좋다. 물론 명시적으로 안해도 크게 문제는 없지만 그럴 경우 GC가 쓰지 않는 데이터를 나중에 참조될 가능성이 있는 데이터로 볼 가능성이 높아진다. 이는 결국 메모리를 많이 잡아먹을 수 있고 결과적으로 프로그램 성능에도 영향을 미친다.

만약 index가 정상적인 참조가 가능한 값일 경우 해당 인덱스의 요소를 반환해준다. 이 때 원본 데이터 타입으로 반환하기 위해 E 타입으로 캐스팅을 해준다.

### 2. remove(Object value) 메서드

&nbsp;&nbsp;&nbsp;remove(Object value) 메서드는 사용자가 원하는 특정 요소(value)를 리스트에서 찾아서 삭제하는 것이다. IndexOf 메서드와 마찬가지로 리스트 안에 특정 요소와 매칭되는 요소가 여러 개 있을 경우 가장 먼저 마주하는(가장 앞부분에 있는) 요소만 삭제한다.  
이 메서드에서 필요한 동작은 value와 같은 요소가 존재하는지, 만약에 존재한다면 몇 번째 위치에 존재하는지를 알아야 하는 것과 그 index의 데이터를 지우고 나머지 뒤의 요소들을 하나씩 당기는 작업, 이렇게 두 가지의 동작이 필요하다.

&nbsp;&nbsp;&nbsp;그동안 만들었던 메서드들을 이용하여 매우 간단하게 만들 수 있다. 리스트에 특정 요소와 같은 요소의 index를 찾는 작업은 우리가 만들어놓았던 indexOf() 메서드가 있다.
그리고 index를 통해 삭제하는 작업은 앞선 remove(int index) 메서드가 있다. 하나하나 다시 만들 필요없이 기존의 두 가지를 조합하면 된다.

```java
@Override
public boolean remove(Object value) {

	// 삭제하고자 하는 요소의 인덱스 찾기
	int index = indexOf(value);

	// -1이라면 array에 요소가 없다는 의미이므로 false 반환
	if (index == -1) {
		return false;
	}

	// index 위치에 있는 요소를 삭제
	remove(index);

	return true;

}
```

&nbsp;&nbsp;&nbsp;indexOf 메서드를 통해 해당 value와 일치하는 요소를 찾아온다. 만약 -1이라면 일치하는 요소가 없다는 의미이므로 false를 반환하고, 아닐 경우 index에 해당하는 요소를 제거해주고 true를 반환하면 끝이 난다.  
이렇게 요소를 삭제하는 remove 메서드들을 만들어보았다.

```java
@SuppressWarnings("unchecked")
@Override
public E remove(int index) {

	if (index >= size || index < 0) {
		throw new IndexOutOfBoundsException();
	}

	E element = (E) array[index];
	array[index] = null;

	for (int i = index; i < size; i++) {
		array[i] = array[i + 1];
		array[i + 1] = null;
	}

	size--;
	resize();

	return element;
}

@Override
public boolean remove(Object value) {

	int index = indexOf(value);

	if (index == -1) {
		return false;
	}

	remove(index);

	return true;

}
```

## size, isEmpty, clear 메서드 구현

&nbsp;&nbsp;&nbsp;남은 메서드 size, isEmpty, clear이다. 다른 메서드 구현에 비해 간단하기 때문에 바로 만들어보자.

### 1. size() 메서드

&nbsp;&nbsp;&nbsp;ArrayList가 동적으로 할당되면서 요소들을 삽입, 삭제가 많아지면 사용자가 리스트에 담긴 요소의 개수가 몇 개인지 기억하기 힘들다. 더군다나 ArrayList에서 size변수는 private 접근제한자를 갖고 있기 때문에 외부에서 직접 참조를 할 수 없다<sup>[1](#footnote_1)</sup>.  
따라서 size 변수의 값을 반환해주는 메서드인 size()를 만들어줄 필요가 있다. size만 반환해주면 되기 때문에 매우 간단하다.

```java
@Override
public int size() {
	return size; // 요소 개수 반환
}
```

### 2. isEmpty() 메서드

&nbsp;&nbsp;&nbsp;isEmpty() 메서드는 현재 ArrayList에 요소가 단 하나도 존재하지 않고 비어있는지를 알려준다. 리스트가 비어있을 경우 true를, 비어있지 않고 단 하나라도 요소가 존재 할 경우 false를 반환해주면 된다. 즉, size가 요소의 개수였으므로 `size == 0`이면 true, 0이 아니면 false이다. 배열을 모두 순회하여 데이터가 존재하는지 검사해줄 필요가 없다.

```java
@Override
public boolean isEmpty() {
	return size == 0;	// 요소가 0개일 경우 비어있다는 의미이므로 true반환
}
```

### 3. clear() 메서드

&nbsp;&nbsp;&nbsp;clear()는 모든 요소들을 비워버리는 작업이다. 리스트에 요소를 담아두었다가 초기화가 필요할 때 쓸 수 있는 유용한 존재다. 또한 모든 요소를 비워버린다는 것은 요소가 0개라는 말로 size 또한 0으로 초기화해주고, 배열의 용적 또한 현재 용적의 절반으로 줄일 수 있도록 해준다.

&#128073;<b>왜 초기 값이 아니라 절반이죠?</b>  
초기값으로 초기화 해주어도 되나 현재 배열의 용적은 결국 데이터를 해당 용적에 만족하는 조건만큼 썼다는 의미이기 때문이다. 예를 들어 데이터가 1500개라면 용적량은 10부터 2씩 곱해지므로 2560이었을 것이다. 요소들을 모두 초기화 했더라도 앞으로 들어올 데이터들 또한 데이터가 1500개일 가능성이 높다. 즉, 현재 용적량의 기대치에 근접할 가능성이 높기 때문에 일단은 용적량을 절반으로만 줄이는 것이다.

```java
@Override
public void clear() {

	// 모든 공간을 null 처리 해준다.
	for (int i = 0; i < size; i++) {
		array[i] = null;
	}

	size = 0;

	resize();
}
```

모든 배열을 명시적으로 null 처리해주는 것이 좋다.

```java
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
	for (int i = 0; i < size; i++) {
		array[i] = null;
	}

	size = 0;

	resize();
}
```

---

# 부록

## clone, toArray 메서드

&nbsp;&nbsp;&nbsp;이 부분은 조금 더 많은 기능을 원할 때 추가해주면 좋은 메서드들이다. clone은 기존에 있던 객체를 파괴하지 않고 요소들이 동일한 객체를 새로 하나 만드는 것이고 toArray는 리스트를 출력할 때 for-each문을 쓰기 위한 것이다. 사실 Iterable을 implement하여 구현해주면 되지만 내용이 조금 어렵기 때문에 대신 toArray를 만들어보자.

### 1. clone() 메서드

&nbsp;&nbsp;&nbsp;사용하던 ArrayList를 새로 하나 복제하고 싶을 때 쓰는 메서드이다. 단순히 `=` 연산자로 객체를 복사하게 되면 '주소'를 복사하기 때문에 복사한 객체에서 데이터를 조작할 경우 원본 객체까지 영향을 미친다<sup>[2](#footnote_2)</sup>.

&nbsp;&nbsp;&nbsp;이러한 것을 방지하기 위한 '깊은 복사'에 필요한 것이 바로 `clone()`이다. Object에 있는 메서드이지만 접근제어자가 protected로 되어있어 만든 것처럼 사용자 클래스의 경우 Cloneable 인터페이스를 implement 해야한다.  
즉, `public class ArrayList<E> implements List<E>`에 Cloneable도 추가해주어야 한다.

```java
@Override
public Object clone() throws CloneNotSupportedException {

	// 새로운 객체 생성
	ArrayList<?> cloneList = (ArrayList<?>)super.clone();

	// 새로운 객체의 배열도 생성해주어야 함
	cloneList.array = new Object[size];

	// 배열의 값을 복사함
	System.arraycopy(array, 0, cloneList.array, 0, size);

	return cloneList;
}
```

`super.clone()` 자체가 생성자와 비슷한 역할이고 shallow copy를 통해 `new ArrayList()`를 호출하는 느낌이라 제대로 완벽하게 복제하려면 clone한 리스트의 array 또한 새로 생성해서 해당 배열에 copy를 해주어야 한다.

### 2. toArray() 메서드

&nbsp;&nbsp;&nbsp;toArray()는 크게 두 가지가 있다.  
하나는 아무런 인자 없이 현재 있는 ArrayList의 리스트를 객체배열(Object[])로 반환해주는 `Object[] toArray()` 메서드이고, 다른 하나는 ArrayList를 이미 생성된 다른 배열에 복사해주고자 할 때 쓰는 `T[] toArray(T[] a)` 메서드가 있다.

```java
public Object[] toArray() {
	return Arrays.copyOf(array, size);
}

@SuppressWarnings("unchecked")
public <T> T[] toArray(T[] a) {
	if (a.length < size) {
		// copyOf(원본 배열, 복사할 길이, Class<? extends T[]> 타입)
		return (T[]) Arrays.copyOf(array, size, a.getClass());
	}

	// 원본배열, 원본배열 시작위치, 복사할 배열, 복사할배열 시작위치, 복사할 요소 수
	System.arraycopy(array, 0, a, 0, size);
	return a;
}
```

`Object[] toArray()` 메서드의 경우 원본 배열(array)를 요소 개수(size)만큼 복사하여 Object[] 배열을 반환해주는 메서드다. 이 것을 그대로 써서 반환해주면 되기 때문에 크게 어렵지 않다.

&nbsp;&nbsp;&nbsp;두 번째 T[] toArray(T[] a) 메서드를 보자.  
이 부분은 제네릭 메서드로, 우리가 만들었던 ArrayList의 E 타입과는 다른 제네릭이다. E 타입이 Integer라고 가정하고, T 타입은 Object 라고 해보자.

Object는 Integer보다 상위 타입으로, Object 안에 Integer 타입의 데이터를 담을 수도 있다. 이 외에도 사용자가 만든 부모, 자식 클래스 같이 상속관계에 있는 클래스들에서 하위 타입이 상위 타입으로 데이터를 받고 싶을 때 쓸 수 있도록 하기 위함이다. 즉, 상위타입으로 들어오는 객체에 대해서도 데이터를 담을 수 있도록 별도의 제네릭 메서드를 구성하는 것이다.  
그리고, 들어오는 배열(a)가 현재 array의 요소 개수(size)보다 작으면 size에 맞게 a의 공간을 재할당 하면서 array에 있던 모든 요소를 복사한다.

또한 앞서 상위타입에 대해서도 담을 수 있도록 하기 위해 copyOf메서드에서 Class라는 파라미터를 마지막에 넣어준다(`a.getClass()`). 그런 다음 Object[] 배열로 리턴 된 것을 T[] 타입으로 캐스팅하여 반환해주면 된다.

반대로 파라미터로 들어오는 배열의 크기가 현재 ArrayList에 있는 array보다 크다면 앞 부분부터 array에 있던 요소만 복사해서 a배열에 넣고 반환해주면 된다.

&nbsp;&nbsp;&nbsp;마지막으로 지금까지 만든 모든 메서드들을 모은 전체코드를 확인해보자.

```java
import Interface_form.List;
import java.util.Arrays;

public class ArrayList<E> implements List<E>, Cloneable {

	private static final int DEFAULT_CAPACITY = 10;	
	private static final Object[] EMPTY_ARRAY = {};

	private int size;

	Object[] array;

	public ArrayList() {
		this.array = EMPTY_ARRAY;
		this.size = 0;
	}

	public ArrayList(int capacity) {
		this.array = new Object[capacity];
		this.size = 0;
	}

	private void resize() {
		int array_capacity = array.length;

		if (Arrays.equals(array, EMPTY_ARRAY)) {
			array = new Object[DEFAULT_CAPACITY];
			return;
		}

		if (size == array_capacity) {
			int new_capacity = array_capacity * 2;

			array = Arrays.copyOf(array, new_capacity);
			return;
		}

		if (size < (array_capacity / 2)) {
			int new_capacity = array_capacity / 2;

			array = Arrays.copyOf(array, Math.max(new_capacity, DEFAULT_CAPACITY));
			return;
		}
	}

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
		}	else {
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

	@SuppressWarnings("unchecked")
	@Override
	public E get(int index) {
		if(index >= size || index < 0) {
			throw new IndexOutOfBoundsException();
		}

		return (E) array[index];
	}

	@Override
	public void set(int index, E value) {
		if (index >= size || index < 0) {
			throw new IndexOutOfBoundsException();
		}	else {
			array[index] = value;
		}
	}

	@Override
	public int indexOf(Object value) {
		int i = 0;

		for (i = 0; i < size; i++) {
			if (array[i].equals(value)) {
				return i;
			}
		}

		return -1;
	}

	public int lastIndexOf(Object value) {
		for(int i = size - 1; i >= 0; i--) {
			if(array[i].equals(value)) {
				return i;
			}
		}

		return -1;
	}

	@Override
	public boolean contains(Object value) {

		if(indexOf(value) >= 0) {
			return true;
		}	else {
			return false;
		}
	}

	@SuppressWarnings("unchecked")
	@Override
	public E remove(int index) {

		if (index >= size || index < 0) {
			throw new IndexOutOfBoundsException();
		}

		E element = (E) array[index];
		array[index] = null;

		for (int i = index; i < size; i++) {
			array[i] = array[i + 1];
			array[i + 1] = null;
		}

		size--;
		resize();

		return element;
	}

	@Override
	public boolean remove(Object value) {

		int index = indexOf(value);

		if (index == -1) {
			return false;
		}

		remove(index);
		return true;
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
		for (int i = 0; i < size; i++) {
			array[i] = null;
		}
		size = 0;

		resize();
	}

	@Override
	public Object clone() throws CloneNotSupportedException {

		ArrayList<?> cloneList = (ArrayList<?>)super.clone();

		cloneList.array = new Object[size];

		System.arraycopy(array, 0, cloneList.array, 0, size);

		return cloneList;
	}

	public Object[] toArray() {
		return Arrays.copyOf(array, size);
	}

	@SuppressWarnings("unchecked")
	public <T> T[] toArray(T[] a) {
		if (a.length < size) {
			return (T[]) Arrays.copyOf(array, size, a.getClass());
		}

		System.arraycopy(array, 0, a, 0, size);

		return a;
	}

}
```

---
**Reference**

- [공식 문서](https://docs.oracle.com/javase/8/docs/api/)
- <https://st-lab.tistory.com/161>

---

<a name="footnote_1">1</a> : size를 접근할 수 있게 될 경우 size에 사용자가 고의적으로 데이터를 조작할 수 있기 때문이다.  
<a name="footnote_2">2</a> : 이러한 복사를 얕은 복사(shallow copy)라고 한다.
