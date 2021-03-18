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
last_modified_at: 2021-03-18
---

# 생성자 대신 정적 팩터리 메서드를 고려하라  

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

*EX) 자바 컬렉션 프레임워크는 45개의 유틸리티 구현체를 제공하지만 공개하지는 않았다(public이 아니다)  
단 하나의 인스턴스화 불가 클래스인 java.util.Collections에서 정적 팩터리 메서드를 통해  
45개의 구현체를 얻도록 했다.*  

## 4️⃣ 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.  

반환 타입의 하위 타입이기만 하면 어떤 클래스의 객체를 반환하든 상관없다.  
심지어 다음 릴리스에서는 또 다른 클래스의 객체를 반환하든 상관없다.  
클라이언트는 팩터리가 건네주는 개체가 어느 클래스의 인스턴스지 알 수도 없고 알 필요도 없다.

## 5️⃣ 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.

이런 유연함은 서비스 제공자 프레임워크(service provider framework)를 만드는 근간이 된다.  
대표적인 서비스 제공자 프레임워크인 JDBC를 예로 들자면  
DriverManager.getConnection 메소드를 작성하는 시점에는 Connection 인터페이스를 구현한 클래스가  
존재하지 않아도 된다는 것이다.
클라이언트는 어떤 제공자(Mysql, Oracle, SqlServer - 서비스의 구현체)가 사용되건 상관없이 Driver, connection 서비스 인터페이스를 통해 구현체를 사용할 수 있다.   
각각의 DBMS의 벤더사에서 Driver, Connection 인터페이스를 구현하여 제공해줄 것이다.  
**즉 Driver, connection 인터페이스와 실제 그 인터페이스를 구현하는 구현체 클래스가 완전히 분리되어 제공되는 것이다.**  

***서비스 제공자 프레임워크****의 구성요소 ex) JDBC*  

*구현체를 나타내는(represents implementations, 구현체의 동작을 정의하는) ***서비스 인터페이스***  
ex) Connection  
제공자가 구현체를 등록할 때 사용하는 ***제공자 등록 API***   
ex) DriverManager.registerDriver  
클라이언트가 서비스의 인스턴스를 얻을 때 사용하는 ***서비스 접근 API***  
ex) DriverManager.getConnection  
서비스 인터페이스의 인스턴스를 생성하는 팩터리 객체(DriverManager)를 설명해주는 ***서비스 제공자 인터페이스***  
ex) Driver*

사족) Class.forName(각각의 벤더사에서 Driver인터페이스를 구현한 구현체 클래스)를 통해  
클래스를 로드하고 등록해준다. 등록하는 과정은 클래스를 로드할 때 자동으로 실행되는 static 블록에서  
DriverManager.registerDriver를 통해 처리된다.

`브리지 패턴과 의존 객체 주입(DI)가 서비스 제공자 프레임워크 패턴의 변형이라고 한다  
이해가 잘 안되서 나중에 공부해봐야겠다`

정적 팩터리 메서드 방식에도 단점이 있다.

## 1️⃣ 하위 클래스를 만들 수 없다.(상속을 할 수 없다.)

상속을 하기 위해서는 public이나 protected 생성자가 필요하기 때문이다.  
그러나 이 제약은 상속보다 컴포지션을 사용하도록 유도하고(Item 18) 불변 타입으로 만들려면 이 제약을
지켜야 한다는 점에서 장점으로 받아들일 수도 있다.

## 2️⃣ 하위 클래스를 만들 수 없다.(상속을 할 수 없다.)

생성자처럼 API 설명에 명확히 드러나지 않으니 사용자는 정적 매서드 방식 클래스를 인스턴스화할 방법을 알아내야 한다.
![image](https://user-images.githubusercontent.com/68231412/111687943-40361b80-886e-11eb-9edd-a091b94bc2e4.png)
그림에서 보다시피 메소드와 달리 생성자는 한번에 구분해서 볼 수 있는 장점이 있다.

## 정적 팩터리 메소드에 사용하는 명명 방식 

**from**: 매개 변수를 하나 받아서 해당 타입의 인스턴스를 반환하는 형변환 메서드  
``` java  
ex) Date d = Date.from(instant);
```

**of**: 여러 매개변수를 받아 적합한 타입의 인스턴스를 반환하는 집계 메서드  
``` java  
ex) Set(Rank) faceCards = EnumSet.of(JACK, QUEEN, KING);
```

**valueof**: from과 of의 더 자세한 버전  
``` java  
ex) BigInteger prime = BigInteger.valueOf(Integer.Max_VALUE);
```

**instance 혹은 getInstance**: (매개변수를 받는다면) 매개변수로 명시한 인스턴스를 반환하지만, 같은 인스턴스임을 보장하지는 않는다.  
``` java  
ex) StackWalker luke = StackWalker.getInstance(options)
```

**create 혹은 newInstance**: instance 혹은 getInstance와 같지만, 매번 새로운 인스턴스를 생성해 반환함을 보장한다.
``` java
ex) Object newArray = Array.newInstance(classObject, arrayLen);
```

**getType**: getInstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할 때 쓴다.
"Type"은 팩터리 메서드가 반환할 객체의 타입이다.
```
ex) FileStore fs = Files.getFileStore(path);
```

**newType**: newInstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할 때 쓴다.
"Type"은 팩터리 메서드가 반환활 객체의 타입이다.
```
ex) BufferedReader br = Files.newBufferedReader(path);
```

**type**: getType과 newType의 간결한 버전
```
ex) List<Complaint> litany = Collections.list(legacyLitany);
```

___
참고(Reference)

[이펙티브 자바 01. 정적 팩토리 메소드와 서비스 제공자 인터페이스 (JDBC 예제)](https://plposer.tistory.com/61)  
[[Java] Class.forName(String className) 그리고 Service Provider Interface](https://devyongsik.tistory.com/294)
[[Java 궁금증] Class.forName()은 어떻게 동작할까?](https://kyun2.tistory.com/23)  
[클래스로더 1, 동적인 클래스 로딩과 클래스로더](https://javacan.tistory.com/entry/1)
