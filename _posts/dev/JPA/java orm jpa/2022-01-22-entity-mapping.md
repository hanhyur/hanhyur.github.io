---
layout: post
date: 2022-01-22 18:44:00
title: "엔티티 매핑"
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