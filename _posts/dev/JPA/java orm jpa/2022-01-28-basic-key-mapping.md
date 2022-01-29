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

Table 전략에서 allocationSize = 50이고, 서버가 여러 대인 경우에는 문제가 없을까?