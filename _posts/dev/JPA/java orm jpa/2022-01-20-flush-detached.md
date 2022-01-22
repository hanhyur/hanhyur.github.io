---
layout: post
date: 2022-01-18 21:29:00
title: "영속성 관리 - 내부 동작 방식 2부"
description: "자바 ORM 표준 JPA 프로그래밍"
subject: JPA
category: [ jpa ]
tags: [ spring, java, orm, jpa ]
use_math: true
comments: true
---

[자바 ORM 표준 JPA 프로그래밍](https://inf.run/JEwj)을 공부하고 정리하는 포스트입니다.

---

# 플러시

플러시는 영속성 컨텍스트의 변경 내역을 데이터베이스에 반영하는 것을 말합니다. 보통 트랜잭션이 커밋될 때 발생하는데, 복잡한 것은 아니고 쌓아 두었던 SQL들이 데이터 베이스에 넘어가는 것입니다.

플러시가 발생해도 1차 캐시는 지워지지 않습니다.

## 플러시 발생

플러시가 발생되면 어떤 일이 생길까요? 변경 감지(더티 체킹)가 일어나고, 수정된 엔티티를 쓰기 지연 SQL 저장소에 등록합니다. 그리고 이어서 쓰기 지연 SQL 저장소의 쿼리를 데이터 베이스에 전송합니다.

플러시가 발생한다고 해서 데이터 베이스 트랜잭션이 커밋되는 것은 아닙니다.

## 영속성 컨텍스트를 플러시하는 방법

- em.flush : 직접 호출
- 트랜잭션 커밋 : 플러시 자동 호출
- JPQL 쿼리 실행 : 플러시 자동 호출

```java
Member member = new Member(200L, "member200");

em.persist(member);

em.flush();

System.out.println("======================");
tx.commit();
```

플러시가 바로 실행되기 때문에 구분선 이전에 쿼리가 발생하는 것을 볼 수 있습니다.

<img src="/assets/img/jpa/jpa28.png" width="70%" align="center"><br/>

JPQL 쿼리가 실행될 때 플러시가 호출되는 이유를 간단한 예제로 알아보겠습니다.

```java
em.persist(memberA);
em.persist(memberB);
em.persist(memberC);

query = em.createQuery("select m from Member m", Member.class);
List<Member> members = query.getResultList();
```

`persist()`만 되어 있는 상태에서는 DB에 아직 저장되지 않은 상태입니다. 이 상태에서 JPQL을 실행해서 데이터를 읽어오려고 하면 어떻게 될까요? 당연히 데이터베이스에는 데이터가 없기 때문에 오류가 발생하게 됩니다. 이러한 경우를 막기 위해서 기본적으로 JPQL이 실행될 때 무조건 플러시가 호출되도록 되어있는 것입니다.

## 플러시 모드 옵션

`em.setFlushMode(FlushModeType.COMMIT)`와 같은 형태로 사용할 수 있습니다.

- FlushModeType.AUTO : 커밋이나 쿼리를 실행할 때 플러시(기본 값)
- FlushModeType.COMMIT : 커밋을 할 때만 플러시. 즉, 쿼리가 실행될 때는 플러시 하지 않음

---

# 플러시는!

- 영속성 컨텍스트를 비우지 않습니다.
- 영속성 컨텍스트의 변경 내용을 데이터베이스에 동기화합니다.
- 트랜잭션이라는 작업 단위가 중요합니다 -> 커밋 직전에만 동기화하면 됩니다.

---

# 준영속 상태

영속 상태가 되는 경우가 `persist()` 외에 `find()`를 했을 때도 있습니다. `find()`를 통해 조회했는데 1차 캐시에 없으면 DB에서 가져와서 1차 캐시에 올리는데 이 올라간 상태가 바로 영속 상태입니다. JPA가 관리하는 상태가 된 것입니다.

준영속 상태는 영속 상태의 엔티티가 영속성 컨텍스트에서 분리된 것, 즉 다 빼버린 상태입니다. 이 상태에서는 영속성 컨텍스트가 제공하는 기능을 사용하지 못하게 됩니다.

준영속 상태로 만드는 방법들을 보겠습니다.

```java
...

public class JpaMain {

  public static void main(String[] args) {
    
    ...
    
    try {

      Member member = em.find(Member.class, 150L);
      member.setName("AAAAA");

      em.detach(member);

      System.out.println("======================");
      tx.commit();
    } ...

  }

}
```

`find()`를 이용해서 찾아온 엔티티를 `setName()`을 이용해서 변경했습니다. 만약 이 상태로 실행 시켰다면 select와 update가 출력되어야 합니다. 하지만 중간에 `em.detach()`를 사용해서 분리시켰습니다. 더 이상 JPA가 관리하지 않는 상태가 되었고, 그 결과 select로 가져는 왔지만 update가 되지는 않았습니다.

<img src="/assets/img/jpa/jpa29.png" width="70%" align="center"><br/>

물론 이러한 것을 직접 사용하는 경우는 잘 없습니다.

이렇게 영속 상태에서 빠지는 것을 준영속 상태가 된다고 합니다. 이렇게 만들어주는 방법은 다음과 같습니다.

- em.detach(entity) : 특정 엔티티만 준영속 상태로 전환
- em.clear() : 영속성 컨텍스트를 완전히 초기화
- em.close() : 영속성 컨텍스트를 종료

---

# 정리

## JPA에서 가장 중요한 2가지

- 객체와 관계형 데이터베이스 매핑하기(Object Relational Mapping)
- 영속성 컨텍스트

## 영속성 컨텍스트

- 엔티티를 영구 저장하는 환경
- 눈에 보이지는 않는 논리적인 개념
- 엔티티 매니저를 통해서 접근
- 일반 환경(J2SE)에서는 1 : 1
- 컨테이너 환경에서는 N : 1

## 엔티티 생명주기

- 비영속
- 영속
- 준영속
- 삭제

## 영속성 컨텍스트의 이점

- 1차 캐시
- 동일성 보장
- 트랜잭션을 지원하는 쓰기 지연
- 변경 감지
- 지연 로딩

## 플러시

- 영속성 컨텍스트의 변경 내용을 데이터베이스에 반영
- 변경 감지
- 커밋이나 쿼리를 실행할 때 발생
- 직접 플러시도 가능
- 영속성 컨텍스트를 비우지 않음
  - 변경 내용을 데이터베이스에 동기화
- 트랜잭션이라는 작업 단위가 중요
  - 커밋 직전에만 동기화하면 됨