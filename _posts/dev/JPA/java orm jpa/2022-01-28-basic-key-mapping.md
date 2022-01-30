---
layout: post
date: 2022-01-28 23:44:00
title: "엔티티 매핑 2부"
description: "자바 ORM 표준 JPA 프로그래밍"
subject: JPA
category: [ jpa ]
tags: [ spring, java, orm, jpa ]
use_math: true
comments: true
---

[자바 ORM 표준 JPA 프로그래밍](https://inf.run/JEwj)을 공부하고 정리하는 포스트입니다.

---

## 기본 키 매핑

기본 키 매핑에서 사용할 수 있는 애노테이션은 크게 `@Id`와 `@GeneratedValue` 2가지 입니다. 

기본 키를 직접 할당하고 싶을 때는 `@Id`만 사용하면 됩니다. 예를 들어, 여러 가지 코드성 데이터를 조합해서 직접 Id를 만들어 할당할 때 `@Id`만 사용해서 만들면 됩니다.

하지만 보통 DB를 사용하면 DB가 자동으로 만들어주는 값을 사용하게 됩니다. 이러한 자동 생성은 `@GeneratedValue` 애노테이션을 사용하면 됩니다.

```java
...

@Id
@GeneratedValue(strategy = GenerationType.AUTO)
...

```

자동 생성(할당)에는 4가지 전략이 있는데 애노테이션 뒤에 'strategy'로 사용할 수 있습니다.

### AUTO

첫 번째 전략은 'AUTO'입니다. AUTO는 기본 값으로, DB 방언에 맞춰서 자동으로 생성이 됩니다. 자동이기 때문에 조금씩 변경되는 점을 확인하면서 사용해야 합니다.

### IDENTITY

```java
@GeneratedValue(strategy = GenerationType.IDENTITY)
```

IDENTITY는 기본 키 생성을 데이터베이스에 위임하는 것입니다. 단적인 예로, MySQL의 'AUTO_INCREMENT'가 있습니다. 주로 MySQL, PostgreSQL, SQL Server, DB2에서 사용합니다.

```java
public class Member {

  @Id
  @GeneratedValue(strategy = GenerationType.IDENTITY)
  private Long id; 

}
```

실제로 실행을 해서 생성될 때 쿼리를 보면 Id 값이 null로 들어가는 것을 볼 수 있습니다. 쉽게 생각하자면 "난 모르겠으니 DB 니가 알아서 해줘" 입니다.

JPA는 보통 트랜잭션 commit 시점에 INSERT SQL을 실행합니다. 하지만 IDENTITY 전략은 `entityManager.persist()` 시점에 즉시 INSERT SQL을 실행하고 DB에서 식별자를 조회합니다.

### IDENTITY 전략 - 특징

IDENTITY 전략은 id 값을 설정하지 않고, 즉 null 값을 담은 INSERT Query를 날리면 그때 id의 값을 세팅합니다. AUTO_INCREMENT는 DB에 INSERT SQL을 실행한 이후에 id 값을 알 수 있는데 이 말은 DB에 값이 들어간 이후에 id 값을 알 수 있다는 것입니다.

여기서 문제점이 발생합니다. JPA의 영속성 컨텍스트에서 관리되려면 무조건 PK 값이 있어야 합니다. 그런데 이 PK 값을 DB에 들어가봐야 알 수 있는 문제가 발생합니다. 즉, IDENTITY 전략에서 영속성 컨텍스트의 1차 캐시 안에 있는 @Id 값은 DB에 넣기 전까지 세팅을 할 수 없다는 것입니다.

그래서 이를 해결하기 위해 어쩔 수 없이 예외적으로 `em.persist()`를 호출한 시점에 바로 INSERT SQL을 날려버립니다. 다른 전략에서는 commit하는 시점에 SQL을 날립니다.

이러한 이유때문에 쿼리를 모아서 INSERT하는게 불가능하다는게 단점입니다. 하지만, 버퍼링해서 Write 하는 것이 큰 이득이 있지 않기 때문에 크게 신경쓰지 않다도 괜찮습니다. 하나의 Transaction 안에서 여러 INSERT Query가 네트워크를 탄다고 해서 엄청나게 비약적인 차이가 나지 않기 때문입니다.

### SEQUENCE

```java
@GeneratedValue(strategy = GenerationType.SEQUENCE)
```

SEQUENCE 전략은 보통 ORACLE 같은 데이터베이스에서 많이 사용하는데, `create sequence`로 Sequence Object를 만들어냅니다. 이 Sequence Object를 통해 값을 가져와서 세팅하는 것입니다.

이름을 주지 않으면 기본 Sequence인 Hibernate Sequence를 사용하는데, 만일 테이블마다 Sequence Object를 따로 관리하고 싶다면 `@SequenceGenerator`를 사용해서 매핑을 하면됩니다.

```java
@SequenceGenerator(
  name = "MEMBER_SEQ_GENERATOR", 
  sequenceName = "MEMBER_SEQ", // 매핑할 데이터베이스 시퀀스 이름 
  initialValue = 1, allocationSize = 1)
```

### SEQUENCE 전략 - 특징

SEQUENCE 전략은 id 값을 설정하지 않고, generator에 매핑된 Sequence 전략에서 id 값을 얻어옵니다. 해당 Sequence Object는 DB가 관리하는 것이기 때문에 DB에서 id 값을 가져와야 하는데, 이는 IDENTITY 전략과 같은 문제점을 가지고 있습니다.

이를 해결하기 위해서 `MEMBER_SEQ`에서 `call next value for MEMBER_SEQ`라는 쿼리가 발생합니다. 그리고 DB에서 값을 얻어와서 이를 id에 넣어준 후에 영속성 컨텍스트가 동작하게 됩니다. 즉, PK 값만 미리 얻고 실제 트랜잭션이 커밋하는 시점에 INSERT 쿼리가 발생하게 됩니다. 버퍼링 방식이 가능하다는 것입니다.

이 때 성능에 신경을 쓴다면 이러한 고민이 있을 수 있습니다. 네트워크를 자꾸 왔다갔다 하니까 성능 상의 저하가 발생하지 않을까?
SEQUENCE 전략을 사용할 경우, Sequence를 매번 DB에서 가지고오는 과정에서 어쨌든 네트워크를 타기 때문에 성능 상의 저하를 가져올 수 있습니다. 그렇다면 차라리 INSERT Query를 한 번에 날리는 것이 낫지 않을까?하는 생각에서 나온 것이 바로 <b>allocationSize</b> 속성값 입니다.

```
create sequence MEMBER_SEQ start with 1 increment by 50
```

이 옵션을 사용하면 next call을 할 때 미리 DB에 50개를 한 번에 올려 놓고 (DB는 sequence가 51로 세팅됩니다.) 메모리 상에서 1개씩 쓰는 것입니다. 그렇게 사용하다가 50개를 모두 사용하면 그 때 또 next call을 날려서 다시 50개를 올려 놓습니다. (DB는 sequence가 101로 세팅됩니다.)

이런 생각이 들 수 있습니다. 어차피 올려놓을꺼 50보다 더 많이 올려놓으면 안될까?
이론적으로는 더 큰 수로 설정할수록 성능은 좋아집니다. 하지만, 중간에 애플리케이션(웹 서버)를 내리는 시점에 사용하지 않는 seq 값이 날라가게 됩니다. 즉, 중간에 구멍이 생기고 중간의 seq 공백이 큰 문제가 되는 것은 아니지만, 그래도 낭비가 되는 것이므로 적당한 50~100 사이로 설정하는 것이 좋습니다.

### TABLE

```java
@GeneratedValue(strategy = GenerationType.TABLE)
```

TABLE 전략은 키 생성 전용 테이블을 만들어서 DB 시퀀스를 흉내내는 전략입니다. 이 전략의 장점은 모든 데이터베이스에 적용이 가능하다는 것입니다. 테이블을 만들어서 거기서 Key, Generator를 계속 뽑아냅니다.

하지만 아무래도 테이블을 직접 사용하기 때문에 성능이 다소 떨어집니다. 락 이슈나 최적화가 안되있기 때문입니다.

```java
...

@Entity
@TableGenerator(name = "MEMBER_SEQ_GENERATOR",
    table = "MY_SEQUENCES",
    pkColumnValue = "MEMBER_SEQ", allocationSize = 1)
public class Member {

  @Id
  @GeneratedValue(strategy = GenerationType.TABLE,
      generator = "MEMBER_SEQ_GENERATOR")
  private Long id;

  ...
```

실행해보면 테이블이 생성되는 것을 볼 수 있습니다. 해당 테이블을 살펴보면 value 값이 있고 그 value 값을 id 값으로 사용하게 됩니다.

<img src="/assets/img/jpa/jpa36.png" width="60%" align="center"><br/>

운영에서는 TABLE 전략을 쓰기는 다소 부담스럽습니다. 왜냐하면 DB에서 관례로 쓰는 것들이 있기 때문입니다.

TABLE 매핑 전략에는 아래와 같은 속성이 있습니다. TABLE 전략 자체가 자주 쓰이지 않기 때문에 간단하게 보면 됩니다.

<img src="/assets/img/jpa/jpa37.png" width="60%" align="center"><br/>

### Table 전략 - 특징

Table 전략에서 allocationSize = 50이고, 서버가 여러 대인 경우에는 문제가 없을까요?
미리 값을 올려두는 방식이기 때문에 여러 대의 서버가 붙더라도 상관이 없습니다. 웹 서버 10대가 동시에 호출하는 경우, 순차적으로 사용하여 값이 올라가 있을 것입니다.

- A 서버: 1~50
- B 서버: 51~100

---

## 권장하는 식별자 전략

그렇다면 권장하는 식별자 전략은 무엇일까요? 이 부분은 데이터베이스를 먼저 생각해야 합니다. 데이터베이스의 기본 키 제약조건을 생각해봅시다.

기본키 제약 조건은 <b>Null이면 안되고</b>, <b>유일</b>해야하며, <b>변하면 안됩니다</b>. 그런데 이러한 조건을 미래까지 만족하는 자연키는 찾기가 어렵습니다. 애플리케이션의 전체 수명동안 계속 기본 키는 변하면 안됩니다.

이런 자연키를 찾기 어렵기 때문에, 대리키(대체키)를 사용합니다. Generator Value나 랜덤 값과 같이 비즈니스와 상관 없는 값을 사용하는 것을 권장합니다.

권장하는 형태는 `Long + 대체키 + 적절한 키 생성 전략`의 형태를 사용하는 것을 권장합니다.