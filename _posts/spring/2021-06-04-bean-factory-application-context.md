---
layout: post
title:  "[SPRING] Bean Factory 와 ApplicationContext"
date:   2021-06-04 14:30
category: spring
icon: www
keywords: bean factory, application context
image: 
preview: 0
comments: true
---

## Bean Factory 와 ApplicationContext

![](/post-img/spring/bean_factory_hierarchy.png)

`BeanFactory 는 스프링 컨테이너의 최상위 인터페이스`이다. 스프링 빈을 조회/관리하는 역할을 담당하고, getBean()을 제공한다.<br>
하지만 어플리케이션을 개발할때에는 빈에 대한 조회/관리 이외에도 많은 기능을 필요로 한다.<br>
때문에 일반적으로 `여러 인터페이스를 상속받아서 기능을 제공하는 ApplicationContext 를 주로 사용`한다.<br>

- MessageSource
- EnvironmentCapable
- ApplicationEventPublisher
- ResourceLoader

등등<br>
<br>


## BeanDefinition

![](/post-img/spring/beandefinition.png)
![](/post-img/spring/beandefinition_1.png)
![](/post-img/spring/beandefinition_2.png)

AnnotationConfigApplicationContext는 `AnnotatedBeanDefinitaionReader`를 통해 <span style="color:red">`Bean 메타정보를 생성한다.(BeanDefinitionRegistry)`</span><br>
스프링 컨테이너는 이 메타정보를 통해 Bean을 생성한다. 즉, Bean 을 생성하는 코드가 Java 인지 XML 인지 알 필요가 없다.<br>
실제로 XML 을 통해 Bean 을 설정할때는 GenericXmlApplicationContext 를 사용한다. 이 때에도 최종적으로 BeanDefinitionRegistry 를 통해 메타정보가 생성되고,<br>
스프링 컨테이너는 이 메타정보만 참조한다.<br>
<br>

BeanDefinition 에는 대략 아래와 같은 정보가 있다.

- BeanClassName: 생성할 빈의 클래스 명(자바 설정 처럼 팩토리 역할의 빈을 사용하면 없음)
- factoryBeanName: 팩토리 역할의 빈을 사용할 경우 이름, 예) appConfig
- factoryMethodName: 빈을 생성할 팩토리 메서드 지정, 예) memberService
- Scope: 싱글톤(기본값)
- lazyInit: 스프링 컨테이너를 생성할 때 빈을 생성하는 것이 아니라, 실제 빈을 사용할 때 까지 최대한 생성을 지연처리 하는지 여부
- InitMethodName: 빈을 생성하고, 의존관계를 적용한 뒤에 호출되는 초기화 메서드 명
- DestroyMethodName: 빈의 생명주기가 끝나서 제거하기 직전에 호출되는 메서드 명
- Constructor arguments, Properties: 의존관계 주입에서 사용한다. (자바 설정 처럼 팩토리 역할 의 빈을 사용하면 없음)
<br>

> Reference : <a href="https://www.inflearn.com/course/스프링-핵심-원리-기본편">인프런 [스프링 핵심 원리 - 기본편] by 김영한</a>