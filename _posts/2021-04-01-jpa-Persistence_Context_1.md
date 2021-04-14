---
layout: post
title:  "[JPA] 영속성 컨텍스트(Persistence Context) (1)"
date:   2021-04-01 11:00
category: jpa
icon: www
keywords: jpa, persistence context, persistence
image: 
preview: 0
comments: true
---

## <span style="color:red">영속성 컨텍스트란?</span>
> 영속성 컨텍스트란 논리적인 개념으로 `"Entity 를 영구 저장하는 환경"` 이라는 의미이다.<br>
어플리케이션과 DB 사이에서 Entity 를 관리하고, EntityManager 를 통해 접근이 가능하다.


### <u>JPA에서 Entity 는 다음과 같은 생명주기를 같는다.</u>
![](/post-img/jpa/entity_lifecycle.PNG)

- 비영속(new)<br>
객체를 생성만 하고 아직 영속성 컨텍스트에서는 관리하지 않는 상태

```java
Member member = new Member();
member.setId("member1");
```

- 영속(managed)<br>
persist를 통해 영속성 컨텍스트에서 관리되고 있는 상태

```java
Member member = new Member();
member.setId("member1");

EntityManager em = emf.createEntityManager();
em.getTransaction().begin();
em.persist(member);
```

- 준영속(detached)<br>
영속성 컨텍스트에서 분리해서 더이상 관리하지 않게함
-> 영속성 컨텍스트에서만 사라질 뿐 DB에 영향을 주지는 않는다.<br>
Entity 를 준영속 상태로 만드는 방법은 아래와 같다.
  - em.detach(entity) : 특정 Entity 만 준영속 상태로 변환
  - em.clear() : 영속성 컨텍스트를 완전히 초기화
  - em.close() : 영속성 컨텍스트를 종료

```java
em.detach(member);
```

- 삭제(removed)<br>
Entity 를 영속성 컨텍스트와 DB에서 삭제

```java
em.remove(member);
```

<br>
### <u>영속성 컨텍스트가 Entity 를 관리하는 방식은 다음과 같다.</u>

- 등록/삭제
<br>
![](/post-img/jpa/persistence_context_persist.PNG)

등록의 경우, persist() 함수를 호출해서 Entity 를 영속성 컨텍스트에서 관리하는 상태로 만든다.<br>
이때 해당 Entity 는 `1차 캐시` 에 저장되고, `쓰기 지연 SQL 저장소`에 Insert SQL 이 생성된다.<br>
삭제의 경우, 1차캐시의 변동 없이 쓰기 지연 SQL 저장소에 Delete SQL 이 생성된다.<br>
아직 이 당시에는 DB에는 저장되지 않는다.

<br>
![](/post-img/jpa/persistence_context_commit.PNG)

이후 commit() 함수를 호출하면 영속성 컨텍스트 내부에서 `flush` 가 발생하고<br> 
쓰기 지연 SQL 저장소의 SQL을 DB로 전송한다.

- 수정
<br>
![](/post-img/jpa/persistence_context_update.PNG)

1차 캐시에 Entity를 저장할 때, 영속성 컨텍스트는 해당 Entity 의 `스냅샷`을 따로 저장한다.<br>
이후 아래처럼 1차캐시에 저장된 Entity 의 속성을 변경하려 할 경우

```java
Member memberA = em.find(Member.class, "memberA");

memberA.setUsername("member_a");
memberA.setAge(10);

transaction.commit();
```
`flush` 가 발생할 때 `변경감지(Dirty Checking)`가 발생하고,<br>
변경된 내용을 쓰기 지연 SQL 저장소에 update SQL 로 작성한다.

- 조회
<br>
![](/post-img/jpa/persistence_context_find.PNG)

조회의 경우 1차 캐시에 해당 Entity 가 있는지 우선 체크 한다.<br>
1차 캐시에 존재할 경우 해당 Entity 를 반환하고,<br>
만약 존재하지 않으면 DB 조회 후 리턴하면서 해당 Entity 를 1차 캐시에 저장한다.


<br>
## <span style="color:red">to be continued...</span>

> Reference : <a href="https://www.inflearn.com/course/ORM-JPA-Basic/dashboard">인프런 [자바 ORM 표준 JPA 프로그래밍 - 기본편] by 김영한</a>