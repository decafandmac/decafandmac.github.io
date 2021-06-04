---
layout: post
title:  "[SPRING] 스프링 컨테이터 생성 및 싱글톤(@Configuration)"
date:   2021-06-04 13:30
category: spring
icon: www
keywords: spring, spring container, bean, application context, singleton, configuration, cgilib
image: 
preview: 0
comments: true
---

## 스프링 컨테이너 생성 과정

### 1. 스프링 컨테이너 생성

![](/post-img/spring/create_container_1.png)

```java
ApplicationContext ac = new AnnotationConfigApplicationConatext(AppConfig.class);
```

스프링 컨테이너 <span style="color:red">`ApplicationContext`</span> 인터페이스의 구현체인 <span style="color:red">`AnnotationConfigApplicationConatext`</span>를 통해 컨테이너를 생성한다.<br>
이는 어노테이션 기반으로 생성한 예시이고, XML 기반으로도 생성은 가능하다.<br>
컨테이너 생성 시 구성 정보를 지정해 주어야 한다.(샘플로 AppConfig.class)<br>
<br>

### 2. 스프링 빈 등록

![](/post-img/spring/create_container_2.png)

스프링 컨테이너는 파라미터로 넘어온 클래스 정보를 사용해서 빈을 등록한다.<br>
<br>

### 3. 스프링 빈 의존관계 설정 - 준비

![](/post-img/spring/create_container_3.png)

<br>

### 4. 스프링 빈 의존관계 설정 - 완료

![](/post-img/spring/create_container_4.png)

스프링 컨테이너는 설정정보를 참고해서 <span style="color:red">`의존관계(DI)를 주입`</span>한다.<br>
일반적으로 스프링은 빈을 생성하고, 의존관계를 주입하는 단계가 나누어져 있다.<br>
하지만 이렇게 자바 코드로 스프링 빈을 등록할 경우 생성자를 호출하면서 의존관계 주입도 한번에 처리된다.<br>

## 스프링 컨테이너와 싱글톤
스프링 컨테이너는 싱글톤 레지스트리이기 때문에 스프링 빈이 싱글톤이 되도록 보장해 주어야 한다.<br>
하지만 스프링이 자바 코드를 조작할 수는 없기 때문에 클래스의 바이트코드 조작 라이브러리를 사용한다.<br>

![](/post-img/spring/singleton_1.png)
![](/post-img/spring/singleton_2.png)

AnnotationConfigApplicationContext 에 파라미터로 넘긴 값도 스프링 빈으로 등록 되기 때문에 AppConfig 도 스프링 빈이다.<br>
그런데 실제로 정보를 출력해보면 <span style="color:red">`xxxCGILIB 가 붙은 다른 클래스`</span>라는 것을 알 수 있다.<br>
`스프링이 CGILIB를 이용해서 AppConfig 를 상속받은 임의의 클래스`를 만들고, 해당 클래스를 스프링 빈으로 등록하면서 설정에 사용했기 때문이다.<br>
바로 이 클래스가 내부의 빈들의 싱글톤을 보장하도록 해준다.<br>
이러한 동작은 <span style="color:red">`@Configuration`</span> 어노테이션 때문에 가능하다.<br>
따라서 `@Configuration 이 없다면 내부에 @Bean 어노테이션이 있다고 하더라도 싱글톤을 보장하지 않는다.`<br>
또한, @Configuration 이 존재 하더라도, `@Bean 어노테이션이 없다면 스프링 빈으로 등록되지 않기 때문에 싱글톤이 보장되지 않는다.`<br>


> Reference : <a href="https://www.inflearn.com/course/스프링-핵심-원리-기본편">인프런 [스프링 핵심 원리 - 기본편] by 김영한</a>