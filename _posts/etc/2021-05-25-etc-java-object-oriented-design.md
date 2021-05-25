---
layout: post
title:  "[ETC] 객체지향 설계에 관해서"
date:   2021-05-25 21:00
category: etc
icon: www
keywords: object, oriented, oop, object orinted
image: 
preview: 0
comments: true
---

## 객체지향 설계에 관해...

객체지향적으로 설계하기 위해서는 `역할`과 `구현`을 분리하는 것이 좋다.
클라이언트는 역할(인터페이스)만 알면 되고 내부 구조는 몰라도 된다. 내부구조나 대상 자체가 변경되어도 영향을 받지 않아야 하기 때문이다.<br>
<br>
자바에서는 <span style="color:red">다형성</span>을 활용해서 역할과 구현을 분리할 수 있다.
- 역할 => 인터페이스
- 구현 => 인터페이스를 구현한 클래스, 구현객체

![](/post-img/etc/oop_design.PNG)
<br>
예시로, 위의 그림처럼 클라이언트인 MemberService 는 역할을 담당하는 MemberRepository 인터페이스의 save() 만 바라보면 된다.<br>
인터페이스 구현이 Memory 를 사용하던, JDBC를 사용하던 클라이언트 입장에서는 변경되는 부분이 없다.<br>
이러한 방식을 통해 유연한 프로그래밍이 가능해 진다.