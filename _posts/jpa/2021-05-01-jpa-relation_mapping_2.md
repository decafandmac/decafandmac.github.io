---
layout: post
title:  "[JPA] 연관관계 매핑(Relation Mapping) (2)"
date:   2021-05-01 00:20
category: jpa
icon: www
keywords: jpa, ManyToOne, OneToMany, OneToOne, ManyToMany, Relation, Relation Mapping, mappedBy
image: 
preview: 0
comments: true
---

## 다대일
> @ManyToOne

가장 많이 사용하는 형태로, 외래키가 있는 쪽을 연관관계의 주인으로 세팅한다.<br>
양방향의 경우 양쪽을 서로 참조하도록<span style="color:red">(mappedBy 사용)</span> 개발해야 한다.

### 다대일 단방향
![](/post-img/jpa/Many_To_One_single.PNG)

### 다대일 양방향
![](/post-img/jpa/Many_To_One_dual.PNG)
<br>

## 일대다
> @OneToMany

일(1) 쪽을 연관관계의 주인으로 하는 매핑관계이다. DB의 경우 항상 다(N) 쪽에 FK가 존재하기 때문에<br>
이 차이에 따라서 일(1) 쪽에 입력 시 의도치 않게 다(N) 쪽에 Update 쿼리가 추가로 실행된다.<br>
또한, @JoinColumn 을 사용하지 않으면 조인테이블 방식을 사용한다.(중간에 조인용 테이블 추가)<br>
<span style="color:red">`-> 유지보수 등등을 편하기 하기 위해서는 다대일 매핑으로 변경하자`</span><br>

일대다 양방향의 경우 공식적으로는 존재하지 않는다.<br>
@JoinColumn(insertable = false, updatable = false) 를 통해 구현할 수는 있지만 사용하지는 말자.

### 일대다 단방향
![](/post-img/jpa/One_To_Many_single.PNG)

### 일대다 양방향
![](/post-img/jpa/One_To_Many_dual.PNG)
<br>

## 일대일
> @OneToOne

주 테이블이나 대상 테이블 모두 외래키를 관리할 수 있다.<br>
여기서 주 테이블의 의미는 업무적으로 주로 많이 접근하는 테이블을 의미한다.<br>
일반적으로 일대일 연관관계의 경우, FK에 <span style="color:red">Unique 제약조건</span>을 걸어서 정합성을 맞춘다.<br>


주 테이블에 FK가 존재하는 경우는 단방향/양방향 모두 @ManyToOne과 유사하다.<br>
FK 가 존재하는 쪽이 연관관계의 주인이 되고, 반대쪽 테이블에는 mappedBy 를 적용해준다.

### 일대일 단방향
![](/post-img/jpa/one_to_one_single.PNG)

### 일대일 양방향
![](/post-img/jpa/one_to_one_dual.PNG)

<span style="color:red">대상 테이블에 FK가 존재하는 경우</span>에는 주 테이블이 연관관계의 주인이 될 수 없다.<br>
따라서 이 경우에는 <span style="color:red">항상 양방향으로 매핑</span>해야 한다.<br>
대상 테이블을 주 테이블로 생각하고 단방향으로 로직을 구현할 수는 있겠지만,<br>
이렇게 되면 이미 주/대상 테이블을 잘못 선정했다고 볼 수 있다.<br>
`결국 일대일 관계에서는 FK가 존재하는 테이블이 무조건 연관관계의 주인`이 되어야 한다.<br>
(참고로 일대다 에서는 FK가 존재하지 않더라도 연관관계의 주인이 될 수 있었다.)<br>

### 일대일 단방향_2
![](/post-img/jpa/one_to_one_single_2.PNG)

### 일대일 양방향_2
![](/post-img/jpa/one_to_one_dual_2.PNG)

일대일 연관관계의 경우 어느 테이블이나 FK를 가질 수 있기 때문에,<br>
아래의 장단점을 따져서 고민해봐야 한다.<br>

- 주 테이블에 FK를 가지는 경우
  - 장점 : 주 테이블만 조회해도 대상 테이블의 데이터가 있는지 확인이 가능하다.
  - 단점 : null 을 갖는 컬럼이 늘어날 수 있다.(대상 테이블에 데이터가 없는 경우)

- 대상 테이블에 FK를 가지는 경우
  - 장점 : 일대다 관계로 변경할 경우 변경이 용이하다.((ex) Member 가 여러개의 Locker 를 갖는 모델로 변경될 경우)
  - 단점 : 프록시 기능의 한계로 <span style="color:red">`지연로딩으로 설정해도 항상 즉시 로딩`</span>된다.<br>
  Member 조회 시 에서 Locker 의 프록시 객체를 생성하기 위해서는 Locker 의 값이 있는지 여부를 알아야 하는데<br>
  <span style="color:red">실제 Member 테이블에는 Locker 와 관련된 컬럼이 없기 때문에 항상 Locker 를 같이 조회 해서 Member 와 연결이 되는지를 확인</span>해야 한다.<br>
  이 때문에 지연로딩이 불가능 하다.<br>

  ## 다대다
> @ManyToMany

일반적으로 관계형 DB는 정규화된 테이블 2개로는 다대다를 표현할 수 없기 때문에<br>
중간에 연결테이블을 추가해서 표현할 수 있다.<br>

### 다대다
![](/post-img/jpa/many_to_many.PNG)

다대다의 경우 <span style="color:red">@JoinTable</span> 어노테이션을 사용해서 연결테이블의 명칭을 지정하고, 양방향/단방향 모두 가능하다.<br>
하지만 다대다의 경우 거의 사용되지 않는데, 이유는 <span style="color:red">연결테이블에는 양쪽 테이블의 ID 이외에는 다른 컬럼을 추가할 수 없기 때문</span>이다.<br>
따라서 만약 다대다 관계가 필요할 경우에는, <span style="color:red">연결테이블을 정식 엔티티로 승격</span> 시키고<br>
<span style="color:red">@OneToMany, @ManyToOne 으로 양쪽으로 관계를 맺는 방식</span>을 사용 한다.<br>

### 다대다_정규테이블 승격
![](/post-img/jpa/many_to_many_2.PNG)

> 주 테이블에 FK가 있는 경우 LAZY 로딩 테스트

```java
@Entity
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@Getter @Setter
public class Member {
    @Id @GeneratedValue
    @Column(name = "member_id")
    private Long id;
    private String name;

    @OneToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "locker_id")
    private Locker locker;

    public Member(String name) {
        this.name = name;
    }
}

@Entity
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@Getter @Setter
public class Locker {
    @Id @GeneratedValue
    @Column(name = "locker_id")
    private Long id;
    private Long number;

    public Locker(Long number) {
        this.number = number;
    }
}


Locker locker1 = new Locker(1L);
lockerRepository.save(locker1);

Member member1 = new Member("member1");
member1.setLocker(locker1);
memberRepository.save(member1);

em.flush();
em.clear();

Member findMember = memberRepository.findById(member1.getId()).get();
/**
 * Locker 의 Proxy 객체(결과 -> findMember = class com.decafandmac.jpatestproject.entity.Locker$HibernateProxy$zY23iqIS)
 */
System.out.println("findMember = " + findMember.getLocker().getClass());
/**
 * Locker 의 정보를 조회하려 할때 실제 객체
 */
System.out.println("findMember = " + findMember.getLocker().getNumber());
```

> 대상 테이블에 FK가 있는 경우 LAZY로 선언 되었지만 EAGER로 로딩 테스트

```java
@Entity
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@Getter @Setter
public class Member {
    @Id @GeneratedValue
    @Column(name = "member_id")
    private Long id;
    private String name;

    @OneToOne(mappedBy = "member", fetch = FetchType.LAZY)
    private Locker locker;

    public Member(String name) {
        this.name = name;
    }

    public void addLocker(Locker locker) {
        this.locker = locker;
        locker.setMember(this);
    }
}

@Entity
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@Getter @Setter
public class Locker {
    @Id @GeneratedValue
    @Column(name = "locker_id")
    private Long id;
    private Long number;

    @OneToOne
    @JoinColumn(name = "member_id")
    private Member member;

    public Locker(Long number) {
        this.number = number;
    }
}


Locker locker1 = new Locker(1L);
lockerRepository.save(locker1);

Member member1 = new Member("member1");
member1.addLocker(locker1);
memberRepository.save(member1);

em.flush();
em.clear();

Member findMember = memberRepository.findById(member1.getId()).get();
/**
 * Locker 의 실제 객체(결과 -> findMember = class com.decafandmac.jpatestproject.entity.Locker)
 */
System.out.println("findMember = " + findMember.getLocker().getClass());
/**
 * Locker 의 정보를 조회하려 할때 실제 객체
 */
System.out.println("findMember = " + findMember.getLocker().getNumber());
```


> Reference : <a href="https://www.inflearn.com/course/ORM-JPA-Basic/dashboard">인프런 [자바 ORM 표준 JPA 프로그래밍 - 기본편] by 김영한</a>