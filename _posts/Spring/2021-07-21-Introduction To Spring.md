---
defaults:
  - scope:
      path: ""
      type: posts
    values:
      layout: single
      author_profile: true
      comments: true
      share: true
      related: true

title: "Intoduction To Spring"
excerpt: "스프링 입문 김영한"
toc: true
toc_sticky: true
toc_label: "목차"
categories:
  - Spring
tags:
  - [Spring, 김영한, 스프링입문]
date: 2021-07-21
last_modified_at: 2021-07-21
---

## 라이브러리

 이전에는 웹 서버(WAS)를 직접 서버에 설치해놓고 (ex. Tomcat) 그 곳에 JAVA 코드를 밀어넣는 식의 시스템이었다. 

이 때는 웹 서버랑 개발 라이브러리가 완전히 분리가 되어있었다. 

Tomcat 서버에 들어가서 라이브러리들을 설치해주어야 했다.  

그러나 지금은 소스 라이브러리에서 웹 서버를 들고 있다(웹 서버를 임베디드, 내장하고 있다.)

라이브러리 하나 빌드해서 서버에 올리면 끝난다. (따로 Tomcat 설치를 하지 않아도 된다.)

- spring-boot-starter-web
  - spring-boot-strater-tomcat: 톰캣 (웹서버)
  - spring-webmvc: 스프링 웹 MVC
- spring- boot-starter-thymeleaf: 타임리프 템플릿 엔진(View)
- spring-boot-starter(공통): 스프링 부트 + 스프링 코어 + 로깅
  - spring-boot
    - spring-core
  - spring-boot-starter-logging
    - logback,slf4j
- spring-bot-starter-test
  - junit: 테스트 프레임워크
  - mockito: 목 라이브러리
  - assertj: 테스트 코드를 좀 더 편하게 작성하게 도와주는 라이브러리
  - spring-test: 스프링 통합 테스트 지원
- spring-boot-devtools
  - html 파일을 컴파일만 해주면 서버 재시작 없이 View 파일 변경이 가능하다.



### 로그 관련 라이브러리

slf4j(인터페이스)  

logback(실제 로그를 어떤 구현체로 출력하는 부분)

이 두가지 조합을 많이 쓴다. (현재 표준)



## VIEW



### 홈페이지 (Welcome Page)

스프링 부트에서 홈페이지 (Welcome Page)는 먼저 static 폴더에서 index.html을 찾아보고 없으면 index 템플릿을 찾아본다.

[Welcome Page 공식문서](https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.developing-web-applications.spring-mvc.welcome-page)



### 템플릿 엔진

스프링 부트는 템플릿 엔진으로 FreeMarker, Groovy, Thymeleaf, Mustache를 지원한다.

[thymeleaf 공식 사이트](https://www.thymeleaf.org/)



#### 작동방식

1. 웹 브라우저에서 url을 입력하면 내장 톰켓 서버로 연결된다.

2. 내장 톰켓 서버는 스프링 컨테이너에 url을 전달한다.

3. 스프링 컨테이너는 @Controller 어노테이션을 한 클래스들 안에서 @GetMapping으로 해당 url 이 입력된 메소드를 찾는다.

4. 조건에 맞는 메소드를 찾으면, 그 메소드는 html 파일 이름을 viewResolver에게 넘긴다.

   이 때 데이터인 담은 model 객체도 같이 넘겨준다.

5. viewResolver는 view를 찾아주고 템플릿엔진에 연결시켜준다.

   - resources:templates/{ViewName}.html

6. 템플릿 엔진은 넘겨진 (넘겨진 Model 데이터와 함께) html 파일을 변환해서 웹 브라우저에게 넘겨준다.



### 정적 컨텐츠

#### 작동방식

1. 웹 브라우저에서 url을 입력하면 내장 톰켓 서버로 연결된다.
2. 내장톰켓 서버는 스프링 컨테이너에서 먼저 전달받은 url을 찾아본다. (컨트롤러가 우선순위를 가진다.)
3. 스프링 컨테이너에서 찾지 못하면 static 폴더에서 해당 html 파일을 찾는다.
4. 찾은 html을 웹 브라우저에게 넘겨준다.

[정적 컨텐츠 공식문서](https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.developing-web-applications.spring-mvc.static-content)



### API

#### 작동방식

1. 웹 브라우저에서 url을 입력하면 내장 톰켓 서버로 연결된다.

2.  @ResponseBody 어노테이션이 붙어있으면 http의 BODY에 직접 데이터를 반환한다.

3. 이 때 HttpMessageConverter가 동작한다.

4. 반환된 데이터가 단순 문자이면 StringConverter가 동작하고, 객체이면 JsonConverter가 동작한다.

   StringConverter 실제 이름은 StringHttpMessageConverter

   JsonConverter 실제 이름은 MappingJackson2HttpMessageConverter

   byte처리 등등 기타 여러 HttpMessageConverter가 기본으로 등록되어 있다.

   이 때 HTTP Accept 헤더가 꼭 XML 타입으로 받아야겠다고 적혀있다면, XmlConverter가 동작한다.

   (HTTP Accept 헤더와 서버의 컨트롤러 반환 타입 정보 둘을 조합해서 HttpMessageConverter가 선택된다.)

5. 바꾼 데이터를 웹 브라우저에게 넘겨준다.



## 웹 애플리케이션 구조

<img width="100%" src="https://user-images.githubusercontent.com/68231412/126516436-73bcdf15-838e-4a0e-841b-d37822b9219e.png" />

- 컨트롤러: 웹 MVC의 컨트롤러 역할
- 서비스: 핵심 비즈니스 로직 구현
- 리포지토리: 데이터베이스에 접근, 도메인 객체를 DB에 저장하고 관리
- 도메인: 비즈니스 도메인 객체, 예) 회원, 주문, 쿠폰 등등 주로 데이터베이스에 저장하고 관리됨



## 테스트

자바의 main 메서드를 통해서 실행하거나 웹 애플리케이션의 컨트롤러를 통해 테스트를 하는 방법도 있다.

하지만 이런 방법들은 준비하고 실행하는데 오래 걸리고, 반복 실행과 여러 테스트를 한번에 실행하기가 어렵다.  

자바는 JUnit이라는 프레임워크로 테스트를 실행해서 이러한 문제를 해결한다.

>  실무에서는 빌드 툴이랑 엮어서, 빌드 툴에서 빌드할 때 테스트 코드를 통과하지 못하면 다음 단계로 넘어가지 못하게 한다. 
> (회원 리포지토리 테스트 케이스 작성 07:30)

각각의 테스트는 서로 의존관계 없이 독립적으로 시행되어야 한다.

이를 위해 afterEach() 나 beforeEach() 등을 이용하자 (ex. 공용 리포지토리를 초기화 해준다)



### TDD란?

[TDD](https://punsoo.github.io/tdd%20&%20clean%20code/TDD/)
