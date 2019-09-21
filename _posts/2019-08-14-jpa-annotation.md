---
layout: post
title: "[JPA] - Annotation"
date: 2019-08-14 20:18:28 -0900
description: JPA 기본적인 Annotation에 대해 정리하였습니다.
categories: web
---

### 필드와 컬럼 매핑

<blockquote>@Column</blockquote>
 컬럼을 매핑하고 제약 조건을 추가할 수 있다.

| 속성 | 기능 | 기본값 |
|--|--|--|
| name | 컬럼 이름 지정 | 객체의 필드 이름 |
| nullable(DDL) | null 값을 허용할지 결정 | true |
| length(DDL) | 문자 길이 제약조건, Strig 타입만 사용 | 255 |
| columnDefinition(DDL) | DB 컬럼 정보를 직접 지정 | 자바 타입과 방언 정보를 사용해 적절한 컬럼 타입을 생성 |
| percision, scale(DDL) | BigDecimal 타입에서 사용한다.(BigInteger) persision은 소수점을 포함한 전체 자리 수, scale은 소수의 자릿수이다. 아주 큰 숫자나 정밀한 소수를 다룰때 사용 | percision = 19, scale = 2 |
| unique(DDL) | 한 컬럼에 간단히 유니크 제약조건을 걸 수 있다. |  |

` table, insertable, updatable은 거의 사용하지 않는다.  `

**name : Column의 이름을 설정한다.
nullable : 비어있는 값을 허용할지 하지 않을지 설정할 수 있다.
length : 해당 Column의 길이를 설정할 수 있다.**
	
<blockquote>@Enumerated</blockquote>
	
자바의 **enum**을 매핑하기 위한 어노테이션

```Java
    @Enumerated(EnumType.STRING)
    private User user;
```

<blockquote>@Temporal</blockquote>

해당 어노테이션을 생략하면@Temporal(TemporalType.TIMESTAMP)와 같다.
자바의 **날짜**를 매핑하기 위한 어노테이션

* TemporalType.DATE : 날짜, DB date 타입과 매핑 (ex) 2019-07-28
* TemporalType.TIME : 시간, DB time 타입과 매핑 (ex) 11:11:11
* TemporalType.TIMESTAMP : 날짜와 시간, DB timestamp 타입과 매핑 (ex) 2019-07-28 11:11:11

```Java
    @Temporal(TemporalType.DATE)
    private Date date;

    @Temporal(TemporalType.TIME)
    private Date time;

    @Temporal(TemporalType.TIMESTAMP)
    private Date timestamp;
```

<blockquote>@Lob</blockquote>

DB에는 기본적으로 VARCHAR 타입을 사용한다.
**필드의 길이 제한이 없이 설정하는 경우**에는 **CLOB 타입**으로 저장해야 한다.
해당 어노테이션을 사용하면 **CLOB, BLOB 타입**을 매핑할 수 있다.

```Java
    @Lob
    private String description;
```

<blockquote>@Transient</blockquote>
    
특정 필드를 **DB에 매핑하지 않는다.**

---

### 기타 매핑

<blockquote>@Access</blockquote>
    
JPA가 엔티티 데이터에 **접근하는 방식**을 지정한다.
@Access를 설정하지 않으면 @Id의 위치를 기준으로 접근한다.

* AccessType.FIELD : 필드 접근
    - 필드에 직접 접근한다.
    - 필드 접근 제어자가 private이어도 접근이 가능하다.

    ```Java
        @Entity
        @Access(AccessType.FIELD) //생략 가능
        public class Member {
            @Id
            private String id;
            ...
    ```

* AccessType.PROPERTY : 프로퍼티 접근
    - 접근자(getter)를 사용한다.

    ```java
        @Entity
        @Access(AccessType.PROPERTY) //생략 가능
        public class Member {
            private String id;
            ...
            
            @Id
            public String getId() {
                return id;
            }
            ...
    ```

* **필드와 프로퍼티** 접근 방식을 함께 사용이 가능하다.

```Java
    @Entity
    public class Member {
    @Id
    private String id;
    
    @Transient
    private String firstName;

    @Transient
    private String lastName;

    @Access(AccessType.PROPERTY)
    public String getFullName() {
        return firstName + lastName;
    }
```