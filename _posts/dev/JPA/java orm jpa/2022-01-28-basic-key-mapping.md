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

