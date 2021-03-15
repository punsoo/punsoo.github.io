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

title: "Effective Java Item 01"
excerpt: "생성자 대신 정적 팩터리 메서드를 고려하라"
toc: true
toc_sticky: true
toc_label: "목차"
categories:
  - Effective Java
tags:
  - [Effective Java, 정적 팩터리 메소드]
date: 2021-03-14
last_modified_at: 2021-03-15
---

#생성자 대신 정적 팩터리 메서드를 고려하라  

## 1️⃣ 이름을 가질 수 있다.  

생성자는 그저 매개변수 타입과 순서로 구분될 뿐이지만,  
정적 팩터리 메서드는 이름을 통해 구체적인 의미를 전달할 수 있다.  
특히, 시그니처가 같은 생성자가 필요할 거 같은 경우에 정적 팩터리 메서드를 사용해주자.

## 2️⃣ 호출될 때마다 인스턴스를 새로 생성하지 않아도 된다.

정적 팩터리 방식의 클래스는 인스턴스 통제(instance-controlled) 클래스이다.

*인스턴스 통제 클래스란?*
*언제 어느 인스턴스를 살아 있게 할지를 철저히 통제할 수 있는 클래스*  

## 3️⃣ 반환 타입의 하위 타입 객체를 반환할 수 있다.

반환할 객체의 클래스를 자유롭게 선택할 수 있는 '엄청난 유연성'을 제공한다.  
구현 클래스를 공개하지 않고도 그 객체를 반환할 수 있어 API를 작게 유지할 수 있다.  

*EX) 자바 컬렉션 프레임워크는 45개의 유틸리티 구현체를 제공하지만, 단 하나의  
java.util.Collections에서 정적 팩터리 메서드를 통해 얻도록 했다.*  

## 4️⃣ 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.  

반환 타입의 하위 타입이기만 하면 어떤 클래스의 객체를 반환하든 상관없다.  
심지어 다음 릴리스에서는 또 다른 클래스의 객체를 반환하든 상관없다.  
클라이언트는 팩터리가 건네주는 개체가 어느 클래스의 인스턴스지 알 수도 없고 알 필요도 없다.

## 5️⃣ 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.

이런 유연함은 서비스 제공자 프레임워크(service provider framework)를 만드는 근간이 된다.  
대표적인 서비스 제공자 프레임워크인 JDBC를 예로 들자면, 클라이언트는 어떤 제공자(Mysql, Oracle, SqlServer)
가 사용되건 상관없이 connection 서비스 인터페이스를 통해 구현체를 사용할 수 있다. 즉 Driver, Connection
인터페이스와 실제 그 인터페이스를 구현하는 구현체 클래스가 완전히 분리되어 제공되는 것이다.  

*서비스 제공자 프레임워크의 구성요소 ex) JDBC*  

*구현체의 동작을 정의하는 서비스 인터페이스  
ex) Connection*  
*제공자가 구현체를 등록할 때 사용하는 제공자 등록 API   
ex) DriverManager.registerDriver*  
*클라이언트가 서비스의 인스턴스를 얻을 때 사용하는 서비스 접근 API  
ex) DriverManager.getConnection*
