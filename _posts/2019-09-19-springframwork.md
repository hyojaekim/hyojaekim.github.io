---
layout: post
title: "[Spring] - IoC Container & Bean"
date: 2019-09-19 19:45:28 -0900
description: 스프링 프레임워크 핵심 기술 강의 중 IoC Container와 Bean에 대해 정리하였습니다.
categories: web
---

<blockquote>Spring IoC Container</blockquote>

- 의존 관계 주입이라고도 하며, 의존 객체를 직접 만들지 않고 주입 받아 사용한다.

- [BeanFactory](https://docs.spring.io/spring-framework/docs/5.0.8.RELEASE/javadoc-api/org/springframework/beans/factory/BeanFactory.html)

- 빈 설정 소스로 부터 빈 정의를 읽어들이고, 빈을 구성하고 제공한다.

  

<blockquote>Bean</blockquote>

- Spring IoC Container가 관리하는 객체
- 장점
  - 의존성 관리
  - 스코프
    - 싱글톤 : 객체를 하나만 만들 수 있는 형태
    - 프로토타입 : 매번 다른 객체를 사용할 수 있는 형태
  - 라이프사이클 인터페이스를 지원한다. 
    - 빈이 추가되면 추가적인 작업을 할 수 있다.
    - @PostConstruct ...