---
layout: post
date: 2022-01-22 18:44:00
title: "엔티티 매핑 1부"
description: "자바 ORM 표준 JPA 프로그래밍"
subject: JPA
category: [ jpa ]
tags: [ spring, java, orm, jpa ]
use_math: true
comments: true
---

[자바 ORM 표준 JPA 프로그래밍](https://inf.run/JEwj)을 공부하고 정리하는 포스트입니다.

---

# 엔티티 매핑

- 객체와 테이블 매핑 : @Entity, @Table
- 필드와 컬럼 매핑 : @Column
- 기본 키 매핑 : @Id
- 연관관계 매핑 : @ManyToOne, @JoinColumn

## 객체와 테이블 매핑

### @Entity

JPA에서는 `@Entity`가 중요합니다. `@Entity`가 붙은 클래스는 JPA가 관리하는 엔티티이고, 이게 없다면 JPA와 관계없는 마음대로 쓰고싶은 클래스입니다. 따라서 JPA를 사용해서 테이블과 매핑할 클래스는 `@Entity`가 필수입니다.

`@Entity`를 사용할 때 주의해야할 점이 있습니다.

먼저, 기본 생성자가 필수로 파라미터가 없는 public이나 protected 생성자로 있어야합니다. 이는 JPA 스펙 상 규정이 되어있습니다. 왜냐하면 JPA 같은 라이브러리가 동적으로 다양한 기술을 사용할 때 필요하기 때문입니다.  
또한 final 클래스, enum, interface, inner 클래스에는 `@Entity`를 붙여서 매핑할 수 없습니다. 마지막으로 DB에 저장하고 싶은 필드에는 final을 사용하면 안됩니다.

### @Entity 속성

`@Entity`의 속성 중에 'name'이라는 속성이 있습니다. 말 그대로 JPA에서 사용할 엔티티 이름을 지정하는 속성이고, 기본 값은 클래스의 이름을 그대로 사용합니다. 일반적으로 변경할 일이 없기 때문에 기본값을 그대로 사용하고 생략합니다.

### @Table

`@Table`은 엔티티와 매핑할 테이블을 지정합니다. 예를 들어 회사 내부적으로 'MBR'이라는 테이블과 매핑해야 된다고 해보겠습니다.

```java
...

@Entity
@Table(name = "MBR")
public class Member {

  ...

}
```

이렇게 하면 DB 테이블 중 MBR과 매핑을 하게 됩니다. `@Table`의 속성은 다음과 같습니다.

<img src="/assets/img/jpa/jpa30.png" width="60%" align="center"><br/>

---

## 데이터베이스 스키마 자동 생성

JPA가 매핑 정보만 보면 어떤 쿼리를 만들지 어떤 테이블인지 알 수 있습니다. 그래서 JPA에서는 아예 애플리케이션 로딩 시점에 DB 테이블을 생성하는 기능도 지원합니다.

이러한 기능으로 JPA는 애플리케이션 로딩 시점에 create문으로 DB를 생성하고 시작할 수 있습니다. 덕분에 테이블 중심이 아닌 객체 중심으로 개발할 수 있습니다. 중요한 것은 데이터베이스 방언을 활용해서 데이터베이스에 맞는 적절한 DDL을 생성해줍니다. 이렇게 생성된 DDL은 운영이 아닌 개발에서만 사용해야 합니다.

### 데이터베이스 스키마 자동 생성 - 속성

<img src="/assets/img/jpa/jpa31.png" width="60%" align="center"><br/>

옵션 중에 `hibernate.hbm2ddl.auto`라는 것이 있는데 이것의 value를 create로 해놓으면 애플리케이션 로딩 시점에 테이블을 생성하는 것을 볼 수 있습니다.

### 데이터베이스 스키마 자동 생성 - 주의

이 기능을 사용할 때 정말 중요한 것이 있습니다. 바로 운영 장비에는 절대 <b>create, create-drop, update</b>를 사용해서는 안됩니다.

개발 초기 단계에서는 create 또는 update로 local과 개발 서버에 사용하면 됩니다.  
이제 개발이 진행되고 테스트 서버(중간 서버)에서는 update 또는 validate를 사용해야합니다. create를 사용하게 되면 누군가가 개발해놓은 데이터가 날아가는 일이 생길 수 있습니다.  
그리고 스테이징과 운영 서버에서는 validate 또는 none으로 사용해야 합니다.

## DDL 생성 기능

JPA 기능 중 `@Column`이라는 것을 사용해서 유니크 제약조건을 줄 수 있습니다.

```java
...

@Entity
public class Member {

  @Id
  private Long id;

  @Column(unique = true, length = 10)
  private String name;

  ...
}
```

이렇게 제약 조건을 추가한다고 해서 런타임이 바뀌거나 하지 않습니다. 애플리케이션에 영향을 주는게 아니라 데이터베이스에 영향을 주는 스크립트가 생성되거나 실행될 뿐입니다. 이렇게 DDL 생성을 도와주는 기능을 DDL 생성 기능이라고 합니다.

`@Table`을 이용하는 것은 런타임에 직접 영향을 주지만, `@Column`을 이용하는 것은 영향을 주지 않습니다. 이처럼 DDL 생성 기능은 DDL을 자동 생성할 때만 사용되고 JPA의 실행 로직에는 영향을 주지 않습니다. 

---

## 필드와 컬럼 매핑

필드와 컬럼 매핑을 하기 위해서 요구사항을 추가해보겠습니다.

1. 회원은 일반 회원과 관리자로 구분해야 한다.
2. 회원 가입일과 수정일이 있어야 한다.
3. 회원을 설명할 수 있는 필드가 있어야 한다. 이 필드는 길이 제한이 없다.

```java
package hellojpa;

import javax.persistence.*;
import java.util.Date;

@Entity
public class Member {

  @Id
  private Long id;

  @Column(name = "name")
  private String username;

  private Integer age;

  @Enumerated(EnumType.STRING)
  private RoleType roleType;

  @Temporal(TemporalType.TIMESTAMP)
  private Date createDate;

  @Temporal(TemporalType.TIMESTAMP)
  private Date lastModifiedDate;

  @Lob
  private String description;

  public Member() {}
}
```

순서대로 살펴보면 `@Id`는 PK를 매핑한 것이고, 객체는 username인데 DB에는 name이라고 사용해야 할 경우 `@Column(name = "")`을 사용해서 지정해 줄 수 있습니다.

그리고 Integer 타입을 사용했는데 이 경우 DB에서 Integer와 가장 적절한 타입으로 생성됩니다. 그리고 DB에서 enum 타입이 없지만 사용하고 싶을 경우에는 `@Enumerated`를 사용하면 됩니다.

날짜 타입을 사용할 때 `@Temporal`이 있는데 DATE, TIME, TIMESTAMP 3가지로 구분됩니다. 보통 DB는 3가지를 구분해서 사용합니다.

DB에 varchar를 넘어서는 큰 컨텐츠를 넣고 싶으면 `@Lob`을 사용하면 됩니다. 실행해보도록 하겠습니다.

<img src="/assets/img/jpa/jpa32.png" width="45%" align="center"><br/>

DB에서 사용하는 타입에 맞춰서 생성된 것을 확인할 수 있습니다.

## 매핑 애노테이션 정리

<img src="/assets/img/jpa/jpa33.png" width="60%" align="center"><br/>

마지막의 `@Transient`는 매핑을 하고싶지 않을 때 사용합니다. 예를 들어 DB와 관계없이 메모리에서만 사용하고 싶다면 이 애노테이션을 사용하면 됩니다.

### @Column

가장 많이 쓰이고 중요한 애노테이션입니다. 

<img src="/assets/img/jpa/jpa34.png" width="60%" align="center"><br/>

### @Enumerated

Enum 타입을 사용할 때, default가 `ORDINAL`입니다. `ORDINAL`은 enum 순서를 데이터베이스에 저장하고, `STRING`은 enum 이름을 데이터베이스에 저장합니다.

주의해야할 점은 `ORDINAL`을 사용하면 안된다는 것입니다. 무조건 `STRING`을 쓸 수 밖에 없는데 `ORDINAL`을 사용해서 '순서'를 저장하게 되면, enum을 생성할 때 순서에 맞춰서 작성을 해야합니다. 만일 순서를 잘못 적용하게 되면 큰 문제가 될 수 있습니다.

### @Temporal

이 애노테이션은 날짜 타입을 매핑할 때 필요합니다. 하지만 Hibernate 최신 버전이 되면서 LocalDate, LocalDateTime을 사용하면 됩니다.

### @Lob

`@Lob`은 지정할 수 있는 속성이 아무것도 없습니다. 매핑하는 필드 타입이 문자면 CLOB으로 매핑되고 나머지는 BLOB으로 매핑이 됩니다.

<img src="/assets/img/jpa/jpa35.png" width="70%" align="center"><br/>

### @Transient

매핑을 하고 싶지않으면 이 애노테이션을 사용하면 됩니다.