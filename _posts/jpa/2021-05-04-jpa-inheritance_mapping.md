---
layout: post
title:  "[JPA] 상속관계 매핑"
date:   2021-05-04 12:42
category: jpa
icon: www
keywords: jpa, 상속관계, 상속관계 매핑, MappedSuperclass, Inheritance, DiscriminatorColumn, DiscriminatorValue
image: 
preview: 0
comments: true
---

## 상속관계 매핑

관계형 데이터베이스는 객체지향의 상속이라는 개념은 없지만 슈퍼타입/서브타입 관계의 모델링 기법이 객체 상속과 유사하다.<br>
공통의 부분을 슈퍼타입으로 모델링하고, 개별적으로 다른 엔티티와 차이가 있는 속성에 대해서는 <br>
각각 서브 엔티티로 표현하는 것이다.<br>

![](/post-img/jpa/super_sub.PNG)

구현 전략은 아래 3가지가 있고, <span style="color:red">부모 엔티티에 @Inheritance 어노테이션</span>을 통해 설정할 수 있다.<br>
- 조인 전략
- 단일테이블 전략
- 구현 클래스마다 테이블 전략

그리고 부모/자식 엔티티에는 각각 아래 어노테이션을 사용할 수 있다.
- @DiscriminatorColumn(name = "DTYPE") : 부모 엔티티에 선언하는 어노테이션으로, 자식 엔티티를 구분하는 용도 이다.
- @DistriminatorValue : 자식 엔티티에 선언하는 어노테이션으로, 부모 엔티티의 자식 구분 컬럼에 들어갈 값을 지정한다. 기본값은 클래스명 이다.

<br>
<br>

### 조인 전략

![](/post-img/jpa/inheritancetype_join.PNG)

조인전략은 객체 모델링과 유사하게 공통 속성을 갖는 엔티티와 개별 속성을 갖는 각각의 엔티티를 나누는 것이다.<br>
이 전략의 경우 <span style="color:red">테이블을 정규화</span>하고 <span style="color:red">저장공간을 효율적</span>으로 사용할 수 있다. 그리고 <span style="color:red">FK참조 무결성 제약조건</span>도 활용 가능하다.<br>
반면 항상 공통/개별 엔티티를 조인해야 하기 때문에 <span style="color:red">성능의 저하</span>가 발생할 수 있고 <span style="color:red">조회쿼리가 복잡</span>해질 수 있다.<br>
그리고 데이터 저장 시 일반적으로 <span style="color:red">Insert 쿼리가 2번 호출</span>된다.(공통/개별)<br>
<br>

### 단일테이블 전략

![](/post-img/jpa/inheritancetype_single.PNG)

이 전략은 모든 속성을 하나의 엔티티에 넣는 것이다.<br>
따라서 별도의 조인이 필요 없으므로 <span style="color:red">조회 성능이 빠르고 쿼리가 단순</span>하다.<br>
하지만 <span style="color:red">컬럼에 null 데이터</span>가 많아질 수 있고, <span style="color:red">테이블이 너무 비대</span>해져서 성능이 급격히 나빠 질 수도 있다.<br>
<br>

### 구현 클래스마다 테이블 전략

![](/post-img/jpa/inheritancetype_each.PNG)

이 전략은 공통속성과 개별속성을 각각 모아서 모두 각각의 엔티티로 만드는 것이다.<br>
not null 제약조건을 사용할 수 있고, 서브타입을 명확하게 구분할 수 있기는 하지만,<br>
여러 자식 테이블을 함께 조회하거나 할때는 union all 을 해야 하기 때문에 쿼리도 복잡해지고 성능도 떨어진다.<br>
<span style="color:red">사실 이 방법의 경우 별로 추천하지는 않는다.</span>
<br>


> Reference : <a href="https://www.inflearn.com/course/ORM-JPA-Basic/dashboard">인프런 [자바 ORM 표준 JPA 프로그래밍 - 기본편] by 김영한</a>