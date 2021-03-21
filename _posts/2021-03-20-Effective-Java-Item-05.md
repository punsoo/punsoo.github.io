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

title: "Effective Java Item 05"
excerpt: "자원을 직접 명시하지 말고 의존 객체 주입을 사용하라"
toc: true
toc_sticky: true
toc_label: "목차"
categories:
  - Effective Java
tags:
  - [Effective Java, 의존 객체 주입]
date: 2021-03-20
last_modified_at: 2021-03-20
---

# 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라

## 1️⃣ 사용하는 자원에 따라 동작이 달라지는 클래스에는 정적 유틸리티 클래스나 싱글톤 방식이 적합하지 않다  

사전(dictionary)에 의존하는 맞춤법 검사기(SpellChecker) 클래스를 생각해보자

정적 유틸리티를 잘못 사용한 예
```java
public class SpellChecker{
	private static final Lexicon dictionary = ...;
	
	private SpellChecker(){} // 객체 생성 방지
	
	public static boolean isValid(String word){...}
	public List<String> suggestions(String type){...}
}
```

싱글톤을 잘못 사용한 예
```java
public class SpellChecker{
	private final Lexicon dictionary = ...;

	private SpellChecker(...){}
	public static SpellChecker INSTANCE = new SpellChecker(...);
	
	public static boolean isValid(String word){...}
	public List<String> suggestions(String type){...}
}
```
위의 두 방식 모두 사전을 단 하나만 사용한다고 가정한다는 점에서 그리 훌륭해 보이지 않다.  
SpellChecker가 여러 사전을 사용할 수 있도록 해보자.  
의존 객체 주입의 한 형태로 인스턴스를 생성할 때 생성자에 필요한 자원을 넘겨주도록 하자.

```java
public class SpellChecker {
    private final Lexicon dictionary;
    
    public SpellChecker(Lexicon dictionary){
    	this.dictionary = Objects.requireNotNull(dictionary);
    }
    
    public static boolean isVaild(String word) {...}
    public static List<String> suggestions(String typo) {...}
}
```

의존 객체 주입은 유연성과 테스트 용이성을 높여준다.  
예에서는 dictionary 라는 딱 하나의 자원만 사용하지만 자원이 몇 개든 의존 관계가 어떻든 상관없이 잘 작동한다.  
또한 불변을 보장하여 같은 자원을 사용하려는 여러 클라이언트가 의존 객체들을 안심하고 공유할 수 있다.

## 2️⃣ 생성자에 자원 팩터리를 넘겨줄 수도 있다.

팩터리란 호출할 때마다 특정 타입의 인스턴스를 반복해서 만들어주는 객체를 말한다.
Supplier<T> 인터페이스가 팩터리를 표현한 완벽한 예이다.  
Supplier<T>를 입력으로 받는 메서드는 일반적으로 한정적 와일드 카드 타입을 사용해 팩터리의 타입 매개변수를 제한해야 한다.  

예컨대 다음 코드는 클라이언트가 제공한 팩터리가 생성한 타일들로 구성된 모자이크를 만드는 메소드다.
```java
Mosaic create(Supplier<? extends Tile> tileFactory) { ... }
```

의존 객체 주입이 유연성과 테스트 용이성을 개선해주긴 하지만, 의존성이 수천 개나 되는 큰 프로젝트에서는 코드를 어지럽게 
하기도 한다. 그럴 때는 대거(Dagger), 주스(Guice), 스프링(Spring) 같은 의존 객체 주입 프레임워크를 사용하면 이런 
어질러짐을 해소할 수 있다.