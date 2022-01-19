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

아직까지는 뭔가 아리송합니다. 왜 이런 이상한 메커니즘을 사용하는 것일까요? 곰곰히 생각해보면 애플리케이션과 DB 사이에 중간 계층이 존재하는 것입니다. 이 중간 계층을 사용하여 버퍼링이나 캐싱 등의 이점을 얻어 낼 수 있습니다. 

## 1차 캐시

영속성 컨텍스트는 내부에 1차 캐시라는 것을 가지고 있습니다. 이 1차 캐시를 영속성 컨텍스트라고 이해해도 괜찮습니다.

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

객체를 만들고 그 객체를 `persist()`하는 순간 entityManager 내부의 1차 캐시에 `Map<key, value>`으로 저장됩니다. key는 @Id로 선언한 필드 값, DB의 PK가 저장되고 value는 해당 엔티티 그 자체가 저장됩니다.

<img src="/assets/img/jpa/jpa20.png" width="70%" align="center"><br/>

이렇게 되면 무슨 이점이 있을까요?

## 1차 캐시에서 조회

객체를 저장하고 조회를 해보겠습니다. 

```java
Member findMember = em.find(Member.class, "member1");
```

`find()`를 사용해서 조회를 하면 JPA는 우선 영속성 컨텍스트에서 1차 캐시를 먼저 찾아봅니다. DB가 아닌 1차 캐시를 먼저 확인해서 해당 엔티티가 있으면 캐시에 있는 값을 바로 조회해서 가져옵니다.

이번에는 DB에는 있지만 캐시에는 없는 member2를 조회한다고 가정해보겠습니다.

```java
Member findMember2 = em.find(Member.class, "member2");
```

<img src="/assets/img/jpa/jpa21.png" width="70%" align="center"><br/>

JPA는 1차 캐시에서 조회를 하지만 존재하지 않기 때문에 DB를 조회합니다. 그리고 DB에서 조회한 값을 1차 캐시에 저장한 후 반환합니다. 이 후에 member2를 다시 조회하게 되면 1차 캐시에 있는 member2가 반환됩니다.

사실 이게 큰 도움은 되지 않습니다. EntityManager는 트랜잭션 단위로 생성되고 트랜잭션이 끝나면 종료됩니다. 즉, 요청이 끝나면 영속성 컨텍스트를 지우기 때문에 1차 캐시도 함께 사라집니다. 여러 명이서 사용하는 캐시가 아니기 때문에 성능 이점이 크게 있지는 않습니다.

```java
...

public class JpaMain {

  public static void main(String[] args) {

    ...

    try {

      // 비영속
      Member member = new Member();
      member.setId(101L);
      member.setName("HelloJPA");

      // 영속
      System.out.println("===== Before =====");
      em.persist(member);
      System.out.println("===== After =====");

      Member findMember = em.find(Member.class, 101L);

      System.out.println("findMember.id = " + findMember.getId());
      System.out.println("findMember.name = " + findMember.getName());

      tx.commit();
    } catch (Exception e) {
      ...

}
```

객체를 생성하면서 동시에 조회를 해서 select 쿼리가 발생하는지 확인해보겠습니다. 만일 DB에서 찾는 과정이 있었다면 Select 쿼리가 발생했을텐데 결과에는 없습니다.

<img src="/assets/img/jpa/jpa22.png" width="70%" align="center"><br/>

왜냐하면 DB가 아니라 1차 캐시에 들어있는 값을 가져오기 때문입니다. 이번에는 저장하는 부분을 제외하고 조회만 해보겠습니다.

```java
    ...

    try {

      Member findMember1 = em.find(Member.class, 101L);
      Member findMember2 = em.find(Member.class, 101L);

      tx.commit();
    } ...

```

기존에 id가 101인 데이터가 DB에 들어가있는 상태에서 101을 2번 조회합니다. 그러면 아래의 그림과 같은 시나리오로 진행이 될 것을 예상해볼 수 있습니다.

<img src="/assets/img/jpa/jpa21.png" width="70%" align="center"><br/>

실제로 실행시켜 결과를 보면 select 쿼리가 한 번만 발생한 것을 볼 수 있습니다.

<img src="/assets/img/jpa/jpa23.png" width="70%" align="center"><br/>

처음 select 쿼리가 발생하여 가져온 101의 값을 1차 캐시에 저장하고 2번째 조회를 할 때는 DB가 아닌 1차 캐시에서 가져온 것입니다.

물론 이 기능은 비즈니스가 정말 복잡해서 같은 것을 여러 번 조회할 때는 괜찮지만, 보통은 크게 도움이 되지는 않습니다. 오히려 성능적인 측면보다는 컨셉을 이해하면 컨셉이 주는 객체지향적인 코드를 작성하는데 도움이 있습니다.

## 영속 엔티티의 동일성 보장

자바 컬렉션에서 같은 객체를 꺼낸 것은 같은 래퍼런스를 가지는 것처럼 JPA의 영속성 컨텍스트도 이와 같은 동일성을 보장합니다.

이게 가능한 이유는 1차 캐시가 있기 때문입니다. 1차 캐시로 REPEATABLE READ(반복 가능한 읽기) 등급의 트랜잭션 격리 수준을 데이터 베이스가 아닌 애플리케이션 차원에서 제공해줍니다. 쉽게 생각말해서 같은 트랜잭션 안에서 `==` 비교를 실행하면 동일성이 보장된다고 생각하면 됩니다.

```java
Member findMember1 = em.find(Member.class, 101L);
Member findMember2 = em.find(Member.class, 101L);

System.out.println("result = " + (findMember1 == findMember2));
```

실행했을 때 결과는 true가 나옵니다.

## 엔티티 등록

영속성 컨텍스트가 있음으로서 엔티티를 등록할 때 트랜잭션을 지원하는 쓰기 지연이 가능합니다. `commit()`하기 전까지 쿼리가 날아가지 않는 지연 로딩이 가능한 것입니다.

<img src="/assets/img/jpa/jpa24.png" width="70%" align="center"><br/>

로직이 실행되면서 JPA 내부에서 일어나는 일을 나타낸 그림입니다.
