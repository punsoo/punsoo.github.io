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

title: "Spring Core Basic_2"
excerpt: "스프링 핵심 기본_2"
toc: true
toc_sticky: true
toc_label: "목차"
categories:
  - Spring
tags:
  - [Spring, 김영한, 스프링 핵심 기본]
date: 2021-08-03
last_modified_at: 2021-08-03
---

## 스프링 컨테이너

- ApplicationContext 를 스프링 컨테이너라 한다.

- ApplicationContext는 인터페이스이다. [(BeanFactory 인터페이스를 상속받는다)](https://jhhan009.tistory.com/71)

- 스프링 컨테이너의 2가지 생성 방식

  - XML

    - 컴파일 없이 Bean 설정 정보를 변경할 수 있다.
    - 빈과 의존관계 등을 일일히 설정하는 것이 상당히 번거롭다.
    - 코드를 실행해야 설정 정보 오류를 확인할 수 있다.
    - 스프링 부트 도입 이후 잘 사용하지 않는다.
    - [링크1](https://atoz-develop.tistory.com/entry/Spring-%EC%8A%A4%ED%94%84%EB%A7%81-XML-%EC%84%A4%EC%A0%95-%ED%8C%8C%EC%9D%BC-%EC%9E%91%EC%84%B1-%EB%B0%A9%EB%B2%95-%EC%A0%95%EB%A6%AC) , [링크2](https://ktrlnr.tistory.com/entry/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88Spring-Container%EB%A5%BC-%EB%A7%8C%EB%93%9C%EB%8A%94-2%EA%B0%80%EC%A7%80-%EB%B0%A9%EB%B2%95-XML-Annotation)

  - Annotation 방식 (자바 코드로 설정)

    - 설정 정보를 변경하려면 컴파일이 필요하다.
    - 컴파일러의 도움을 받기 때문에, 오타 등의 설정 정보 오류를 미리 알 수 있다.

  - > 스프링 컨테이너는 BeanFactory, ApplicationContext 2가지를 의미하지만 BeanFactory를 직접 사용하는 경우는 거의 없으므로 일반적으로 ApplicationContext를 스프링 컨테이너라고 한다.

### 스프링 컨테이너의 생성 과정

1. 스프링 컨네이터 생성

   - 스프링 컨테이너 안에는 빈 이름을 KEY 값으로, 빈 객체를 VALUE로 가지는 스프링 빈 저장소가 있다.

   - 스프링 컨테이너를 생성할 때는 구성 정보(XML 파일이나 Configuration 어노테이션을 붙인 자바 클래스)를 넘겨주어야 한다.

2. 스프링 빈 등록

   - 빈 저장소에 빈을 등록해준다.
     - @Configuration 자바 클래스의 경우, @Bean을 붙인 메소드 이름이 빈 이름이 되고 반환 객체가 빈 객체가 된다.
     - 빈 이름을 직접 부여할 수도 있다. ( @Bean(name = "beanName") )
     - 빈 이름은 중복되어서는 안된다. 다른 빈이 무시되거나, 기존 빈을 덮어버려서 설정에 따라 오류가 발생한다.

3. 스프링 빈 의존관계 설정 - 준비

4. 스프링 빈 의존관계 설정 - 완료

   - 설정 정보를 참고해서 의존관계를 주입(DI)한다.

> 스프링 빈을 생성하고, 의존관계를 주입하는 단계가 나누어져 있다.
>
> 하지만 이렇게 자바 코드로 빈을 등록하면 생성자 호출과정에서 의존관계 주입도 한번에 처리된다.

### 스프링 빈 등록 확인

```java
AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
String[] beanDefinitionNames = ac.getBeanDefinitionNames();
for (String beanDefinitionName : beanDefinitionNames) {
	BeanDefinition beanDefinition = ac.getBeanDefinition(beanDefinitionName);

	if (beanDefinition.getRole() == BeanDefinition.ROLE_APPLICATION) {
        Object bean = ac.getBean(beanDefinitionName);
        System.out.println("name = " + beanDefinitionName + " object = " + bean);
    }
}
```

- BeanDefinition.ROLE_APPLICATION : 직접 등록한 애플리케이션 빈
- BeanDefinition.ROLE_INFRASTRUCTURE: 스프링이 내부에서 사용하는 빈

`ac.getBeanDefinitionNames ()`: 스프링에 등록된 모든 빈 이름을 조회한다.

`ac.getBean()`: 빈 이름으로 빈 객체(인스턴스)를 조회한다.

 - `ac.getBean(빈이름, 타입)`
 - `ac.getBean(타입)`

두 가지 방법으로 조회가 가능하다.

`ac.getBeansOfType(SomeType.class)` 을 사용하면 해당 타입의 모든 빈을 조회한다.

## Reference

이 글은 김영한님의 [스프링 핵심 원리 - 기본편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard)을 보고 정리해서 작성하였습니다.

[1] [스프링 코어(3) - 스프링 컨테이너 & 빈](https://jhhan009.tistory.com/71)

[2] [[Spring] 스프링 XML 설정 파일 작성 방법 정리](https://atoz-develop.tistory.com/entry/Spring-%EC%8A%A4%ED%94%84%EB%A7%81-XML-%EC%84%A4%EC%A0%95-%ED%8C%8C%EC%9D%BC-%EC%9E%91%EC%84%B1-%EB%B0%A9%EB%B2%95-%EC%A0%95%EB%A6%AC)

[3] [스프링 컨테이너(Spring Container)를 만드는 2가지 방법 (XML, Annotation) 출처](https://ktrlnr.tistory.com/entry/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88Spring-Container%EB%A5%BC-%EB%A7%8C%EB%93%9C%EB%8A%94-2%EA%B0%80%EC%A7%80-%EB%B0%A9%EB%B2%95-XML-Annotation)

