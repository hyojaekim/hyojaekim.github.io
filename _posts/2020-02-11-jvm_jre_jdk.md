---
layout: post
title: "JVM, JRE, JDK의 차이"
date: 2020-02-11 20:21:35
image: '/assets/img/'
description: 'JVM, JRE, JDK 각각 구분하여 정리하려고 합니다.'
tags:
- java
categories:
- Java
---

## 서론

개발하면서 JVM, JDK, JRE은 자주 듣는 단어입니다. 모른다고 해서 개발하는데 큰 지장은 없지만, 각각의 차이를 알지 못하면 혼란이 오기도 합니다. 이번 글은 각각의 용어에 대해 가볍게 소개하면서 어떤 차이가 있는지 다루도록 하겠습니다.

![JDK, JRE, JVM](/assets/img/jvm_jre_jdk/jvm_jre_jdk.png)

#### \* JVM (Java Virtual Machine)

**`Java Compiler`는 `.java 파일에서 .class로 (Java byte code)로 변환` 합니다.** Byte Code는 기계어가 아니기 때문에 OS가 해석 할 수 없습니다. 따라서 기계어로 변환시켜 OS가 해석할 수 있도록 만들어줘야 합니다. **이때 `Byte Code를 OS가 해석 할 수 있도록 변환`시키는 작업을 `JVM`이 합니다.**

JVM의 단점은 해석하는 과정을 거치기 때문에 `네이티브 언어(c, c++ 등)보다 속도가 느리지만`, 최초에는 한 줄씩 해석하고 실행하는 방식(Interpreter 방식)에서 `Interpreter 방식과 JIT(Just In Time) 컴파일러 방식을 접목해 단점을 보완했습니다.`

#### \* JRE (Java Runtime Environment)

JRE는 **`JVM과 핵심 라이브러리로 구성`**되어, **자바 애플리케이션을 실행할 수 있도록 합니다.** (자바 런타임 환경에서 사용하는 프로퍼티 세팅이나 리소스 파일을 가지고 있습니다)

자바 애플리케이션을 실행할 수 있다고 했는데 이때 개발 관련 도구도 포함되어 있다고 생각할 수 있지만, 개발 관련된 도구는 JDK에서 제공합니다.

#### \* JDK (Java Development Kit)

JDK는 **`JRE + 개발에 필요한 툴로 구성`되어 자바 개발을 위한 필수 도구라고 볼 수 있습니다.**

## 참고

- 백기선 님의 더 자바 "코드를 조작하는 다양한 방법" 강의
- [JVM 이란?](https://medium.com/@lazysoul/jvm-%EC%9D%B4%EB%9E%80-c142b01571f2)
