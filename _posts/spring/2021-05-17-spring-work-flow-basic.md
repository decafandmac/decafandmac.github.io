---
layout: post
title:  "[SPRING] 스프링 웹 작동방식 기본"
date:   2021-05-17 22:15
category: spring
icon: www
keywords: spring, thymeleaf, 정적 컨텐츠, mvc, api, controller
image: 
preview: 0
comments: true
---

## 정적 컨텐츠 호출

![](/post-img/spring/work_flow_basic_static.PNG)

요청한 내용에 해당하는 컨트롤러가 존재하지 않을 경우 정적 컨텐츠 탐색<br>
<br>

## MVC와 템플릿 엔진

![](/post-img/spring/work_flow_basic_mvc.PNG)

컨트롤러가 존재하고, 해당 컨트롤러에서 문자열을 리턴할 경우, viewResolver 가 화면을 찾아 처리함<br>
기본은 <span style="color:red">resuerces 하위 templates 폴더의 리턴문자열.html</span> 대상을 찾는다.<br>
<br>

## API

![](/post-img/spring/work_flow_basic_api.PNG)

### @ResponseBody 문자 반환
```java
@GetMapping("hello-spring")
@ResponseBody
public String helloSpring(@RequestParam("name") String name) {
    return "hello " + name;
}
```

<br>

### @ResponseBody 객체 반환
```java
@GetMapping("hello-api")
@ResponseBody
public String helloApi(@RequestParam("name") String name) {
    Hello hello = new Hello();
    hello.setName(name);
    return hello;
}

static class Hello {
    private String name;

    public String getName() { return name;}

    public String setName(String name) {
        this.name = name;
    }
}
```

@ResponseBody 를 사용할 경우 viewResolver 를 사용하지 않는다. 대신 <span style="color:red">HttpMessageConverter</span> 가 동작한다.<br>
이후, 리턴타입이 문자열일 경우 <span style="color:red">StringHttpMessageConverter</span> 가 작동해서 문자열을 그대로 리턴하고,<br>
객체일 경우 기본적으로 <span style="color:red">MappingJackson2HttpMessageConverter</span> 가 작동해서 JSON 형태로 리턴한다.<br>
<br>

> Reference : <a href="https://www.inflearn.com/course/스프링-입문-스프링부트/dashboard">인프런 [스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술] by 김영한</a>