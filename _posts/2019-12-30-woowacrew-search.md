---
layout: post
title: "JPA Specification을 활용한 검색 기능 구현"
date: 2019-12-30 14:50:28 -0900
description: 우아한테크코스 레벨 4 게시판 검색 기능을 맡으면서 어떻게 문제를 해결했는지 정리해봤습니다.
categories: 우아한테크코스
---

커뮤니티 사이트를 만들면서 다양한 게시판들이 생겼다. 우리는 자유게시판, 크루게시판, 익명게시판, 블로그 피드 게시판이 있는데 각각의 게시판에 검색 기능이 필요했다.

엘라스틱서치를 사용하기에는 러닝 커브도 있고 단기간에 적용하기 힘들 거라고 판단했다.

JPA 쿼리메소드를 통해 간단하게 기능은 구현했다. 하지만 쿼리메소드가 복잡해지고, 각각의 게시판마다 서로 다른 컨트롤러와 레퍼지토리가 존재하면서 많은 중복이 생겼다.

컨트롤러에는 사용자가 어떤 타입을 선택했는지에 따라 분기 처리와 그에 맞는 예외처리로 인해 코드가 더러워졌다.

```java
@GetMapping("/search")
public ResponseEntity<ArticleResponseDtos> search(@PathVariable String searchType, @PathVariable String SearchContent) {
    if (제목 검색이면) {
      ...
    } else if (제목 + 내용으로 검색이면) {
      ...
    } else if (작성자로 검색이면) {
      ...
    }
}
```

이렇게 하면 검색 타입에 대해 변경사항이 생기면 많은 부분을 수정해야 하는 번거로움이 생긴다.

> Specification

구글링과 김영한 님의 JPA 책을 통해 문제를 해결할 수 있었다.

기존에 `findAllByBasicArticleFormTitle()` 이런 식으로 매번 타입에 따른 쿼리메소드를 작성해줘야 한다. 하지만 Specification을 이용하면 외부에서 조건을 설정할 수 있다.

```java
public interface CrewArticleRepository extends JpaRepository<CrewArticle, Long>, JpaSpecificationExecutor<CrewArticle> {
    Page<CrewArticle> findAll(Specification<CrewArticle> specification, Pageable pageable);
}
```

이걸 어떻게 활용해볼까 하다가 `ArgumentResolver & Anotation & Enum & Generic`을 통해 문제를 해결했다.

```java
@GetMapping("/search")
public ResponseEntity<ArticleResponseDtos> search(
        @AllowedSearchType(type = {SearchType.TITLE, SearchType.TITLE_WITH_CONTENT, SearchType.AUTHOR}) SearchSpec<CrewArticle> searchSpec,
```

개발자가 해야 할 일은 딱 2가지이다.

1. `@AllowedSearchType에 허용할 검색 타입을 선언`한다. (Enum 활용)
2. `SearchSpec에 엔티티 타입을 선언`해준다. ex) `SearchSpec<CrewArticle>`

ArgumentResolver를 활용해 컨트롤러에 오기 전, 사용자가 선택한 검색 타입과 검색 내용으로 그에 맞는 Specification을 생성해주기 때문에 컨트롤러에서 사용자가 어떤 타입을 선택했는지, 어떤 내용으로 검색했는지 알 필요가 없다.

이로 인해 분기 처리하는 로직도 사라지고 많은 중복을 제거할 수 있다. 또, 검색 타입을 변경하거나 삭제하는 경우 로직을 수정할 일 없이 변경에 유연하게 만들 수 있는 장점이 있었다.

사용자가 선택한 타입, 내용, 허용하는 검색 타입들을 받아 허용된 검색 타입에서 사용자가 선택한 타입이 있는지 확인하고 Specification을 생성한다.

```java
public static <T> SearchSpec<T> init(String requestType, String requestContent, SearchType[] requestAllowedTypes) {
    AllowedSearchTypes allowedSearchTypes = new AllowedSearchTypes(requestAllowedTypes);
    SearchType searchType = allowedSearchTypes.find(requestType);

    SearchSpec<T> searchSpec = new SearchSpec<>();
    searchSpec.specification = SearchSpecFactory.createLikeSpecification(searchType, requestContent);
    return searchSpec;
}
```

Specification을 생성하는 방법은 아래와 같다. `root`에는 필드의 경로를 설정해줘야 하는데 변환하는 로직은 메소드를 분리했다. `builder`에서 like 메소드를 이용해 필드 경로와 패턴을 넣어주면 `내가 지정한 엔티티 필드의 값에 해당 패턴으로 포함되어 있는지 확인하는` 조건을 추가할 수 있다.

```java
private static <T> Specification<T> createLikeSpecBy(FieldPath fieldPath, String pattern) {
    return (Specification<T>) (root, query, builder) -> {
        Path<String> path = convertPath(root, fieldPath);
        return builder.like(path, pattern);
    };
}
```

> 조건 추가하기

Specification은 서로 이어서 조건을 추가할 수 있는 기능도 존재한다. 네이버 카페에 검색 타입을 보면 제목 + 내용으로 검색하는 타입도 있다.

나도 `제목 + 내용으로 검색`을 추가하기 위해 specification을 이어 붙이는 기능을 추가했다.

`firstSpecification은 제목으로 검색`, `secondSpecification가 내용으로 검색`이라면 or 메소드를 활용해 두 개를 이어 붙일 수도 있다.

```java

    ...

    //제목 또는 내용으로 검색 조건 추가
    return firstSpecification.or(secondSpecification);
}
```

> 문제점

Specification을 만들기 위해 필드의 경로를 알려줘야 하는데 가변인자와 Enum을 통해 해결했지만, Enum이 점점 커지는 느낌인데 더 좋은 방법이 떠오르지 않는다..

```java
...
CONTENT_OF_ARTICLE_FORM_OF_BASIC_ARTICLE_FORM("basicArticleForm", "articleForm", "content"),
NICKNAME_OF_AUTHOR_OF_BASIC_ARTICLE_FORM("basicArticleForm", "author", "nickname"),
DESCRIPTION_OF_FEED_SOURCE("feedSource", "description"),
DEGREE_NUMBER_OF_DEGREE_OF_AUTHOR_OF_BASIC_ARTICLE_FORM("basicArticleForm", "author", "degree", "degreeNumber");

private List<String> fieldPath;

FieldPath(String... fieldPath) {
    this.fieldPath = Arrays.asList(fieldPath);
}
```
