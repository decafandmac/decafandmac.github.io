---
layout: post
title:  "[JPA] 데이터베이스 스키마 자동생성"
date:   2021-04-03 11:30
category: jpa
icon: www
keywords: jpa, hibernate, hbm2ddl
image: 
preview: 0
comments: true
---

## persistence.xml 의 hibernate 속성을 통해 DB 스키마를 어느 시점에 어떻게 생성할 지 지정할 수 있다.
<br>

- create : 테이블 삭제 후 다시 생성(drop + create)
- create-drop : create 와 같으나, 종료시점에 테이블 drop
- update : 변경분만 반영
- validate : Entity 와 Table 이 정상적으로 매핑 되었는지만 확인
- none : 사용하지 않음

생성되는 ddl 의 경우 persistence.xml 에서 설정한 데이터베이스 방언에 따라 달라진다.<br>
`-> org.hibernate.dialect.H2Dialect`

그리고 혹시 모를 미연의 사고를 방지하기 위해 <span style="color:red">운영장비에서는 절대 create, create-drop, update 를 사용하지 않는 것</span>을 추천한다.<br>
- 개발 초기 단계는 create, 또는 update
- 테스트 서버는 update 또는 validate
- 스테이징과 운영 서버는 validate 또는 none


<br>

---
## 매핑 어노테이션 정리

|어노테이션|설명|
|---|---|
|@Column|컬럼 매핑|
|@Temporal|날짜 타입 매핑(LocalDate, LocalDateTime 사용시에는 생략 가능|
|@Enumerated|enum 매핑<br>ORDINAL을 사용할 경우 Enum 에 사용하는 값이 변경되면 DB에 저장된 값의 의미가 섞임<br> -> <span style="color:red">항상 value 값으로는 EnumType.STRING 을 사용한다</span>|
|@Lob|BLOB, CLOB 매핑|
|@Transient|특정 필드를 컬럼에 매핑하지 않음(매핑무시)|

> Reference : <a href="https://www.inflearn.com/course/ORM-JPA-Basic/dashboard">인프런 [자바 ORM 표준 JPA 프로그래밍 - 기본편] by 김영한</a>