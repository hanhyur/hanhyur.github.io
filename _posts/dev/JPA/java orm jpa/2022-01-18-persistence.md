---
layout: post
date: 2022-01-18 21:29:00
title: "영속성 컨텍스트"
description: "자바 ORM 표준 JPA 프로그래밍"
subject: JPA
category: [ jpa ]
tags: [ spring, java, orm, jpa ]
use_math: true
comments: true
---

[자바 ORM 표준 JPA 프로그래밍](https://inf.run/JEwj)을 공부하고 정리하는 포스트입니다.

---

# 영속성 컨텍스트

JPA의 내부구조가 어떻게 동작하는지 좀 더 자세히 알아보겠습니다.

JPA에서 가장 중요한 2가지는 다음과 같습니다.

- 객체와 관계형 데이터베이스 매핑하기 (Object Relational Mapping)
- 영속성 컨텍스트

하나는 정적인 부분이라고 할 수 있는, DB를 설계하고 객체를 설계해서 JAP로 매핑해서 사용하는, 객체와 관계형 DB 매핑 설계와 관련된 부분이고 또 하나 중요한 것은 실제 JPA가 내부에서 어떻게 동작하는가와 관련된 <b>영속성 컨텍스트</b>입니다.

여기서 영속성 컨텍스트를 명확하게 이해하면 JPA의 내부적인 동작을 명확히 이해할 수 있습니다.

## Entity Manager와 Entity Manager Factory

<img src="/assets/img/jpa/jpa17.png" width="70%" align="center"><br/>

예를 들어 웹 애플리케이션을 개발할 때 Entity Manager Factory를 통해서 고객 요청이 올 때마다 Entity Manager를 생성하고, Entity Manager는 내부적으로 데이터 베이스 커넥션을 사용해서 DB를 사용하게 됩니다.

그렇다면 영속성 컨텍스트라는게 도대체 무엇일까요? JPA를 학습하다보면 이 단어를 계속 마주치게 됩니다. 영속성 컨텍스트는 JPA를 이해하는데 가장 중요한 용어입니다.

굳이 한국어로 번역해보자면 <b>엔티티를 영구 저장하는 환경</b>이라는 뜻으로 번역할 수 있는데, 지금까지는 `EntityManager.persist(entity)`와 같이 사용하면 DB에 데이터를 저장하는구나 라고 받아들였습니다.

## 엔티티 매니저? 영속성 컨텍스트?

이 `EntityManager.persist(entity)`는 DB에 저장하는게 아니라 영속성 컨텍스트를 통해서 entity라는 것을 영속화한다는 뜻인데, 더 명확하게 말하면 여기서 사용된 `persist` 메서드는 DB에 저장하는게 아니라 entity를 영속성 컨텍스트라는 곳에 저장한다는 것입니다.

이 영속성 컨텍스트는 논리적인 개념으로 눈에 보이지 않습니다. 따라서 엔티티 매니저를 통해서 영속성 컨텍스트에 접근합니다. 이게 무슨 말일까요?

엔티티 매니저를 생성하면 그 내부에 1:1로 영속성 컨텍스트가 생성이 됩니다. 엔티티 매니저 내부에 보이지 않는 영속성 컨텍스트라는 공간이 생기는 것입니다.

---

# 엔티티 생명주기

엔티티에는 생명주기가 있습니다.

<img src="/assets/img/jpa/jpa18.png" width="70%" align="center"><br/>

## 비영속 (new/transient)

영속성 컨텍스트와 전혀 관계가 없는 새로운 상태로, 최초의 객체를 생성한 상태입니다.

```java
Member member = new Member();
member.setId("member1");
member.setUsername("회원1");
```

## 영속 (managed)

생성한 객체를 `persist`하면 영속상태가 됩니다. 영속성 컨텍스트에 관리되는 상태입니다.

```java
/* 객체 생성 : 비영속 */
Member member = new Member();
member.setId("member1");
member.setUsername("회원1");

EntityManager em = emf.createEntityManager();
em.getTransaction().begin();

/* 객체 저장 : 영속 */
em.persist(member);
```

여기서 한 가지 짚고 넘어가야 할 점이 있습니다. 영속 상태가 된다고해서 바로 DB에 쿼리가 날라가지 않는다는 것인데, 실행 결과를 잠깐 보겠습니다.

```java
...

public class JpaMain {

  public static void main(String[] args) {
    ...

    try {

      // 비영속
      Member member = new Member();
      member.setId(100L);
      member.setName("HelloJPA");

      // 영속
      System.out.println("===== Before =====");
      em.persist(member);
      System.out.println("===== After =====");

      tx.commit();
    } catch (Exception e) {
      ...

}
```

<img src="/assets/img/jpa/jpa19.png" width="70%" align="center"><br/>

`persist()`가 before와 after 사이에 있는데 아무런 일이 없고, before, after와 관계없이 뒤에서 쿼리가 발생했습니다.

그러면 언제 쿼리가 날아갈까요? 트랜잭션을 커밋하는 시점에 영속성 컨텍스트에 있는 정보들이 DB에 쿼리로 날아갑니다.

## 준영속 (detached)

준영속은 `detach()`라는 메서드를 사용하며 엔티티가 영속성 컨텍스트에서 분리된 상태입니다.

## 삭제 (removed)

마지막으로 삭제는 말 그대로 DB 삭제를 요청한 상태입니다. 실제 영구 저장한 데이터를 DB에서 지우는 상태입니다.

---

# 영속성 컨텍스트의 이점

아직까지는 뭔가 아리송합니다. 왜 이런 이상한 메커니즘을 사용하는 것일까요? 곰곰히 생각해보면 애플리케이션과 DB 사이에 중간 계층이 존재하는 것입니다.

바로 이 중간 계층을 사용하여 큰 이점을 얻어 낼 수 있습니다.