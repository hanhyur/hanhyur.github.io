---
layout: post
date: 2021-11-30 21:13:00
title: "스프링 핵심 원리 이해1 1부"
description: "스프링 핵심 원리 - 기본편"
subject: Spring
category: [ spring ]
tags: [ spring, basic, oop ]
use_math: true
comments: true
---

[스프링 핵심 원리 - 기본편](https://inf.run/bMZx)을 공부하고 정리하는 포스트입니다.

---

# 스프링 핵심 원리 이해1 - 예제 만들기

이제 예제를 만들어보면서 공부할텐데 이 때 순수한 자바만으로 만들어보고, 실제 요구사항이 변경되었을 때 다형성, OCP, DIP가 잘 지켜지는지 확인할 것이다.

## 프로젝트 생성

- Java 11 설치
- IDE : IntelliJ

[스프링 부트 스타터 사이트](https://start.spring.io)로 이동해서 프로젝트를 생성한다.

프로젝트 사양은 다음과 같다.

<img src="/assets/img/springcore/core01.png" width="70%" align="center"><br/>

이 때 dependencies를 넣지 않는데 이렇게 할 경우 스프링 코어 쪽 라이브러리와 간단한 요소들로 구성을 해주기 때문이다.

Generate를 눌러서 압축파일을 받으면 IDE를 실행시켜 프로젝트를 열면 된다.

실행시킨 후 라이브러리를 살펴보면 정말 핵심적인 것들만 들어있는 것을 확인할 수 있다. 그리고 프로젝트를 실행시켜서 아래와 같이 나오면 잘 생성된 것이다.

<img src="/assets/img/springcore/core02.png" width="70%" align="center"><br/>

여기까지 했다면 프로젝트 세팅은 끝이다.

---

## 비즈니스 요구사항과 설계

비즈니스 요구사항은 다음과 같다.

- 회원
  - 회원가입과 조회
  - 회원은 일반과 VIP 두 가지 등급이 있다.
  - 회원 데이터는 자체 DB를 구축할 수도 있고, 외부 시스템과 연동할 수 있다. (미확정)

- 주문과 할인 정책
  - 회원은 상품을 주문할 수 있다.
  - 회원 등급에 따라 할인 정책을 적용할 수 있다.
  - 할인 정책은 모든 VIP는 1000원을 할인해주는 고정 금액 할인을 적용. (변경 가능)
  - 할인 정책은 변경 가능성이 높다. 최악의 경우 적용하지 않을 수도 있다. (미확정)

요구사항을 보면 결정하기 어려운 부분도 있는데, 그렇다고 계속 미룰 수는 없다. 객체 지향 설계를 통해서 인터페이스를 만들고 구현체를 언제든지 교체할 수 있도록 설계하면 된다.

---

## 회원 도메인 설계

- 회원 도메인 요구사항
  - 회원 가입을 하고 조회할 수 있다.
  - 회원은 일반과 VIP 두 가지 등급이 있다.
  - 회원 데이터는 자체 DB를 구축할 수 있고, 외부 시스템과 연동할 수 있다. (미확정)

<img src="/assets/img/springcore/core03.png" width="70%" align="center"><br/>

객체 다이어그램에서 회원 서비스는 `MemberServiceImpl`로 인터페이스이다.

도메인 협력 관계는 기획자들도 볼 수 있는 것으로 개발자들이 이것을 구체화하여 클래스 다이어그램을 만들어낸다. 클래스 다이어그램은 실제 서버를 실행하지 않고 클래스만 분석한 것이기 때문에 어떤 구현체가 적용될지는 알 수가 없다. 구현체들은 동적으로 결정되기 때문에 실제 서버가 실행될 때 결정된다. 클래스 다이어그램만으로는 판단하기 힘들기 때문에 만들어낸 것이 객체 다이어그램이다.

## 회원 도메인 개발

### 회원 엔티티

다음과 같은 코드를 작성한다.

```java
package hello.core.member;

public enum Grade {
  BASIC,
  VIP
}
```

```java
package hello.core.member;

public class Member {

  private Long id;
  private String name;
  private Grade grade;

  public Member(Long id, String name, Grade grade) {
    this.id = id;
    this.name = name;
    this.grade = grade;
  }

  public Long getId() {
    return id;
  }

  public void setId(Long id) {
    this.id = id;
  }

  public String getName() {
    return name;
  }

  public void setName(String name) {
    this.name = name;
  }

  public Grade getGrade() {
    return grade;
  }

  public void setGrade(Grade grade) {
    this.grade = grade;
  }
}
```

## 회원 저장소

### 회원 저장소 인터페이스

```java
package hello.core.member;

public interface MemberRepository {

  void save(Member member);

  Member findById(Long memberId);

}
```

### 메모리 회원 저장소 구현체

```java
package hello.core.member;

import java.util.HashMap;
import java.util.Map;

public class MemoryMemberRepository implements MemberRepository {

  private static Map<Long, Member> store = new HashMap<>();

  @Override
  public void save(Member member) {
    store.put(member.getId(), member);
  }

  @Override
  public Member findById(Long memberId) {
    return store.get(memberId);
  }
}
```

이 때 `HashMap`의 경우 동시성 이슈가 있을 수 있기 때문에, `ConcurrentHashMap`을 사용하는 것이 좋지만 예제이기 때문에 `HashMap`을 사용한다<sup>[1](#footnote_1)</sup>.

## 회원 서비스

회원 서비스는 두 가지가 있어야 한다. 가입, 조회.

### 회원 서비스 인터페이스

```java
package hello.core.member;

public interface MemberService {

  void join(Member member);

  Member findMember(Long memberId);

}
```

### 회원 서비스 구현체

```java
package hello.core.member;

public class MemberServiceImpl implements MemberService {

  private final MemberRepository memberRepository = new MemoryMemberRepository();

  @Override
  public void join(Member member) {
    memberRepository.save(member);
  }

  @Override
  public Member findMember(Long memberId) {
    return memberRepository.findById(memberId);
  }

}
```

여기까지 회원 도메인의 개발이 끝났다. 다음에는 회원 도메인 실행과 테스트를 해보겠다.

---
<a name="footnote_1">1</a> : 참조 <https://devlog-wjdrbs96.tistory.com/269>, <https://woooongs.tistory.com/67>