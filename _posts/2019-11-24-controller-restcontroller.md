---
layout: post
title: "[Spring] - @Controller, @ResponseBody, @RestController"
date: 2019-11-24 14:50:28 -0900
description: 스프링 @Controller, @ResponseBody, @RestController 어노테이션 관계에 대해 정리하였습니다.
categories: Spring
---

![controller-main](/assets/img/controller/controller-main.png)

스프링에서 컨트롤러에 대한 어노테이션은 `@Controller`와 `@RestController`가 있다. 두 어노테이션의 차이점은 전통적인 스프링 MVC 컨트롤러에서 HTTP **Response Body 생성 방식**에 따라 구분된다.

<blockquote>@Controller(Spring MVC Controller)</blockquote>

전통적인 스프링 MVC 컨트롤러 `@Controller`는 주로 **View를 반환**하기 위해 사용하며, 아래와 같은 과정으로 흘러간다.

![controller-flow](/assets/img/controller/controller-flow.png)

@Controller는 주로 View를 반환하기 위해 사용하지만, **@ResponseBody를 사용하여 객체를 반환할 수도 있다.**

![controller-code](/assets/img/controller/controller-code.png)

localhost:8080/good2로 이동해보면 아래와 같은 결과가 나온다.

![controller-code-result](/assets/img/controller/controller-code-result.png)

<blockquote>@ResponseBody</blockquote>

`@ResponseBody`를 사용하면 **Spring에서 HTTP 응답에 값을 자동으로 변환해주며, 주로 데이터를 반환하기 위해 사용한다.**

![responsebody-flow](/assets/img/controller/responsebody-flow.png)

@ResponseBody는 위와 같은 과정으로 흘러간다. @Controller와 다르게 @ResponseBody는 **View를 거치지 않고, Controller에서 직접 데이터를 리턴하는 것이 특징이다.**

@ResponseBody는 각 메소드 마다 작성해야 하는 번거로움이 있다. 이 문제를 해결하기 위해 @RestController가 등장했는데 @RestController에 대해 한번 알아보자.

<blockquote>@RestController(Spring Restful Controller)</blockquote>

`@RestController`는 **@ResponseBody와 같이 주로 데이터를 반환하기 위해 사용하며, 아래와 같이 @ResponseBody와 동일한 과정으로 흘러간다.**

![RestController-flow](/assets/img/controller/restcontroller-flow.png)

@RestController는 4버전에서 도입되어 **기존의 @Controller와 @ResponseBody를 결합한 형태이다.**

![RestController-code-1](/assets/img/controller/restcontroller-code-1.png)
