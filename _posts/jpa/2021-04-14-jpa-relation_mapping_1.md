---
layout: post
title:  "[JPA] 연관관계 매핑(Relation Mapping) (1)"
date:   2021-04-14 11:05
category: jpa
icon: www
keywords: jpa, ManyToOne, OneToMany, OneToOne, ManyToMany, Relation, Relation Mapping, mappedBy
image: 
preview: 0
comments: true
---

## 연관관계 매핑

> 일반적으로 DB테이블은 FK 를 이용한 Join 을 통해 서로 간에 연관관계를 맺는다.<br>
이러한 내용을 객체지향 모델링에 반영하여 `객체의 참조와 DB의 FK를 반영하는 것을 연관관계 매핑`이라 한다.<br>

## 연관관계의 주인
DB를 객체와 매핑할때는 <span style="color:red">DB의 FK에 해당하는 항목을 어느 객체에서 관리할 것인가</span>가 중요한 문제가 된다.<br>
이 FK를 관리하는 객체를 <span style="color:red">연관관계의 주인</span>이라 하는데<br>
명확하게 <span style="color:red">`FK가 포함된 DB의 테이블과 매핑된 객체를 연관관계의 주인`</span>으로 설정하면 된다.<br>
이 주인 객체에서만 FK를 관리(등록, 수정) 하고, 양방향일 경우 반대 객체에서는 <span style="color:red">`mappedBy`</span>를 통해 읽기만 가능하도록 처리한다.<br>

이때 중요한 것은 <span style="color:red">`주인이라고 해서 비즈니스 적으로 더 중요하다는 의미는 아니다.`</span><br>
단지 모델링 적으로 봤을 때 FK를 관리하는 객체라는것 이외에 비즈니스 적인 의미는 없다.

만약 반대로 세팅할 경우 아래처럼 의도치 않은 작동을 할 수 있다.<br>
![](/post-img/jpa/relation_mapping_1_N_single.PNG)
```java
public class Member {
    private Team team;
}

public class Team {
    @OneToMany
    @JoinColumn(Name = "TEAM_ID")
    private List<Member> members = new ArrayList<Member>();
}

// Member member1 = new Member();
// member1.setName("member1");
// em.persist(member1);

// Team team = new Team();
// team.setName("TeamA");
// team.addMember(member1); -> Member 테이블에 update 쿼리가 나감
// em.persist(team);
```
Member 테이블의 FK 관리를 Team 객체에서 하고 있기 때문에,<br>
<span style="color:red">Team 데이터 추가 시 전혀 관계없는 Member 에 Update 쿼리가 발생</span>하게 된다.<br>
이처럼 나중에 테이블 구조나 프로그램 구조가 복잡해지는 상황에서 예상할 수 없는 쿼리의 실행이 발생할 수 있기 때문에<br>
되도록이면 위 형태는 사용하지 않는 것이 좋다.<br>

## 단방향/양방향 연관관계
객체의 연관관계는 DB와는 다르게 <span style="color:red">단방향, 양방향</span>이라는 방향성이 존재한다.<br>
DB의 경우 한쪽 테이블에 FK 가 존재하면 어느 테이블에서든 join 을 통해 서로간에 참조가 가능하지만<br>
객체의 경우 참조하고자 하는 객체의 참조가 존재하지 않으면 상대방을 참조할 수 없다.<br>
즉, <span style="color:red">양방향 연관관계는 단방향 연관관계 2개가 합쳐진 모양</span>이 된다.

![](/post-img/jpa/relation_mapping_N_1_dual.PNG)
```java
public class Member {
    private Team team; // Team 객체를 조회하기 위한 단방향 연관관계
}

public class Team {
    private List<Member> members = new ArrayList<Member>(); // Member 객체를 조회하기 위한 단방향 연관관계
}
```
위 예시를 보면 2개의 단방향 연관관계가 합쳐져서 서로간의 참조가 가능한 양방향 연관관계를 생성하고 있음을 알 수 있다.<br>

양방향 연관관계 시 흔히 하는 실수로<br>
한 쪽에만(주로 연관관계의 주인이 아닌 객체) 값을 입력하는 경우가 있다.<br>

객체지향 관점에서 봤을 때 양쪽 모두 값을 입력하는 것이 맞기 때문에<br>
아래 예시와 같이 편의 메서드를 만들어서 양쪽에 입력할 수 있도록 하자.<br>
```java
public class Team {
    private List<Member> members = new ArrayList<Member>();

    // 양쪽에 모두 값 입력
    public void addMember(Member member) {
        members.add(member);
        member.add(this);
    }
}
```

그리고,<br>
일반적으로는 양방향 관계의 경우 고려해야 할 부분들이 많기 때문에<br>
일단은 기본적으로 단방향 관계로 모두 세팅하고, __*필요할 경우에만 양방향 관계를 추가*__ 하는 것이 좋다.

<br>
## <span style="color:red">to be continued...</span>

> Reference : <a href="https://www.inflearn.com/course/ORM-JPA-Basic/dashboard">인프런 [자바 ORM 표준 JPA 프로그래밍 - 기본편] by 김영한</a>