---
layout: post
title:  "[JPA] 기본키 매핑 방법"
date:   2021-04-06 11:30
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
때문에 IDENTITY 전략의 경우 <span style="color:red">persist() 시점에 즉시 INSERT SQL 이 실행된다.</span>

- <u>SEQUENCE 전략</u><br>
주로 Oracle, PostgreSQL, DB2, H2 등에서 사용한다.<br>
<span style="color:red">@SequenceGenerator</span> 를 통해 DB시퀀스를 생성하고, 해당 시퀀스의 nextval 값에 의해 세팅된다.

```java
@SequenceGenerator(
    name = "Member_SEQ_GENERATOR",
    sequenceName = "MEMBER_SEQ",
    initialValue = 1,
    allocationSize = 1
)
public class Member {
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "Member_SEQ_GENERATOR")
    private Long Id;
}
```

|속성|설명|기본값|
|---|---|---|
|name|식별자 생성기 이름(프로그램 내부 이름)|필수
|sequenceName|DB에 등록되는 시퀀스 이름|hibernate_sequence
|initialValue|시퀀스 초기값|1|
|allocationSize|시퀀스 증가 값<span style="color:red">(이 값을 반드시 1로 해야 함)</span>|<span style="color:red">50</span>
|catalog, scheme|DB catalog, scheme 이름|


- <u>TABLE 전략</u><br>
키 생성 전용 테이블을 하나 만들어서 시퀀스와 유사하게 작동하도록 하는 방식이다.<br>
모든 DB에 적용 가능하다는 장점은 있지만<br>
키를 생성하기 위해 해당 테이블에 DML 이 이루어져야 하기 때문에 성능이 떨어지는 단점이 있다.

```java
@TableGenerator(
    name = "Member_SEQ_GENERATOR",
    sequenceName = "MY_SEQUENCES",
    pkColumnValue = "MEMBER_SEQ",
    allocationSize = 1
)
public class Member {
    @Id
    @GeneratedValue(strategy = GenerationType.TALBE, generator = "Member_SEQ_GENERATOR")
    private Long Id;
}
```