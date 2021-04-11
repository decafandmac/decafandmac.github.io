---
layout: post
title:  "[JPA] 기본키 매핑 방법"
date:   2021-04-11 23:30
category: jpa
icon: www
keywords: jpa, GeneratedValue, SequenceGenerator, GenerationType, strategy
image: 
preview: 0
---

## 기본키 매핑 방법

- 직접할당 : @Id 만 사용 -> id 변수에 직접 값 세팅
- 자동생성(@GeneratedValue)
  - IDENTITY : DB에 위임
  - SEQUENCE : DB 시퀀스 사용(`@SequenceGenerator` 필요)
  - TABLE : 키 생성용 테이블 사용(`@TableGenerator` 필요)
  - AUTO : 방언에 따라 자동 지정(기본값)


---

- <u>IDENTITY 전략</u><br>
주로 MySQL, PostgreSQL, SQL Server, DB2 등에서 사용한다.<br>
IDENTITY 전략의 경우 DB에 위임하기 때문에 DB Insert 시점에 해당 값을 알 수 있고,<br>
때문에 IDENTITY 전략의 경우 <span style="color:red">persist() 시점에 즉시 INSERT SQL 이 실행된다.</span><br>
`영속성 컨텍스트에 객체가 관리되기 위해서는 ID값이 필수`이기 때문이다.

- <u>SEQUENCE 전략</u><br>
주로 Oracle, PostgreSQL, DB2, H2 등에서 사용한다.<br>
이름을 지정하지 않을 경우 기본 시퀀스가 생성 되고<br>
임의의 시퀀스를 사용하고자 할 경우 <span style="color:red">@SequenceGenerator</span> 를 통해 DB시퀀스를 생성하고, 해당 시퀀스의 nextval 값에 의해 세팅된다.<br>
<br>
persist() 시점에 DB에 <span style="color:red">시퀀스</span>만 조회해서 next val을 가져오고 이 값을 이용해서 id 를 세팅하고 영속성 컨텍스트에 넣는다.<br>
-> <span style="color:red">실제 영속성 컨텍스트에 객체를 넣을 때는 insert 쿼리가 나가지 않음</span>

```java
@SequenceGenerator(
    name = "MEMBER_SEQ_GENERATOR",
    sequenceName = "MEMBER_SEQ",
    initialValue = 1,
    allocationSize = 1
)
public class Member {
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "MEMBER_SEQ_GENERATOR")
    private Long Id;
}
```

```java
try {
    Member member1 = new Member();
    member1.setName("member1");

    System.out.println("===========");
    em.persist(member1);
    System.out.println("===========");

    tx.commit();
}

// persist() 시점에는 시퀀스만 호출함
// commit() 시점에 insert 실행
// ===========
// Hibernate: 
//     call next value for hibernate_sequence
// ===========
// Hibernate: 
//     /* insert domain.Member
//         */ insert 
//         into
//             Member
//             (name, id) 
//         values
//             (?, ?)
```

|속성|설명|기본값|
|---|---|---|
|name|식별자 생성기 이름(프로그램 내부 이름)|필수
|sequenceName|DB에 등록되는 시퀀스 이름|hibernate_sequence
|initialValue|시퀀스 초기값|1|
|allocationSize|시퀀스 증가 값|<span style="color:red">50</span>
|catalog, scheme|DB catalog, scheme 이름|

allocationSize 가 1 이상일 경우, <span style="color:red">지정해놓은 값 만큼 DB에서 미리 값을 올려 놓고</span><br>
그 사이 구간의 값은 DB 호출 없이 사용한다.(Table 전략도 동일함)<br>
이를 통해 DB에 접근하는 횟수를 줄일 수 있기 때문에 `성능 최적화`가 가능할 수 있다.
<br><br>
(ex) allocationSize = 2 인 경우, 아래처럼 작동한다.

```java
try {
    Member member1 = new Member();
    member1.setName("member1");

    Member member2 = new Member();
    member2.setName("member2");

    Member member3 = new Member();
    member3.setName("member3");

    Member member4 = new Member();
    member4.setName("member4");

    System.out.println("===========1");
    em.persist(member1);
    System.out.println("===========2");
    em.persist(member2);
    System.out.println("===========3");
    em.persist(member3);
    System.out.println("===========4");
    em.persist(member4);
    System.out.println("===========5");

    tx.commit();

// ===========1
// Hibernate: 
//     call next value for MEMBER_SEQ -> 최초 시퀀스 1 조회
// Hibernate: 
//     call next value for MEMBER_SEQ -> (1 + allocationSize) 값 조회 -> 3
// ===========2 -> 미리 조회해온 시퀀스 값 사용 (DB 접근 안함)
// ===========3 -> 미리 조회해온 시퀀스 값 사용 (DB 접근 안함)
// ===========4 -> 미리 조회해온 시퀀스 값을 넘어섰기 때문에 한번 더 조회 함
// Hibernate: 
//     call next value for MEMBER_SEQ
// ===========5
// Hibernate: 
//     /* insert domain.Member
}
```


- <u>TABLE 전략</u><br>
키 생성 전용 테이블을 하나 만들어서 시퀀스와 유사하게 작동하도록 하는 방식이다.<br>
모든 DB에 적용 가능하다는 장점은 있지만<br>
키를 생성하기 위해 해당 테이블에 DML 이 이루어져야 하기 때문에 성능이 떨어지는 단점이 있다.

```java
@TableGenerator(
    name = "MEMBER_SEQ_GENERATOR",
    table = "MY_SEQUENCES",
    pkColumnValue = "MEMBER_SEQ",
    allocationSize = 1
)
public class Member {
    @Id
    @GeneratedValue(strategy = GenerationType.TALBE, generator = "MEMBER_SEQ_GENERATOR")
    private Long Id;
}
```

위와 같은 코드가 실행되면, 아래의 create 문이 생성된다.(h2 DB 기준)<br>
```sql
create table MY_SEQUENCE (
    sequence_name varchar(255) not null,
    next_val bigint,
    primary key (sequence_name)
)
```
그리고 값으로 sequence_name 에는 `MEMBER_SEQ`, next_val에는 `1(초기값)`이 들어간다.

|속성|설명|기본값|
|---|---|---|
|name|식별자 생성기 이름(프로그램 내부 이름)|필수
|table|키 생성 테이블명|hibernate_sequences
|pkColumnName|시퀀스 컬럼명|sequence_name
|pkColumnValue|키로 사용할 값 이름|엔티티 이름
|initialValue|초기값|1
|allocationSize|시퀀스 한번 호출에 증가하는 수(성능 최적화에 사용됨)|<span style="color:red">50</span>
|catalog, scheme|DB catalog, scheme 이름|
|uniqueConstraint(DDL)|유니크 제약 조건을 지정할 수 있음|