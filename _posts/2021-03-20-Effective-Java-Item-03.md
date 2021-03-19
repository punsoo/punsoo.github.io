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

title: "Effective Java Item 03"
excerpt: "private 생성자나 열거 타입으로 싱글톤임을 보증하라"
toc: true
toc_sticky: true
toc_label: "목차"
categories:
  - Effective Java
tags:
  - [Effective Java, 싱글톤]
date: 2021-03-20
last_modified_at: 2021-03-20
---

# private 생성자나 열거 타입으로 싱글톤임을 보증하라

싱글톤(singleton)이란 인스턴스를 오직 하나만 생성할 수 있는 클래스를 말한다.
그런데 클래스를 싱글톤으로 만들면 이를 사용하는 클라이언트를 테스트하기가 어려워질 수 있다.
테스트를 하기 위해서는 가짜(mock) 구현으로 대체해야 하는데 2가지 문제점이 있다.

### 1. 생성자가 private이기 때문에 상속할 수 없고 테스트에서 싱글톤 인스턴스의 생성을 통제할 수 없다.
### 2. getInstance 메소드의 경우 static 이기 때문에 가짜 객체를 싱글톤 대신 주입하기 힘들다.
mocking frameworks는 상속(Inheritance)과 다형성(polymorphism)을 기반으로 하고 있는데 2가지 모두 문제가 된다.  
`사실 다형성이 왜 문제가 되는지는 아직 이해가 안된다... `  
 [setter 메소드를 추가해서 테스트 ](https://blog.jayway.com/2010/01/15/learn-to-stop-worrying-and-love-the-singleton/) 가능하게 하거나
[interception과 AOP concept에 기반한 최신 mocking framework ](http://www.weblogism.com/item/254/mocking-static-method-calls) 를 이용하는 방법이 있겠다.   
[PowerMock](https://github.com/powermock/powermock) 을 쓰는 것도 하나의 방법이다.  
(PowerMock을 쓰면 일반적으로 mocking이 힘든 static, fianl, private 메소드 mocking이 가능하다)

여기서는 인터페이스를 구현하는 싱글톤에 대해서 자세히 알아보자.  
```java
interface Singleton {
    private static final myInstance;
    public static Singleton getInstance() { return myInstance; }
    public static void setInstance(Singleton newInstance) { myInstance = newInstance; }
    // public method declarations
}

// Used in production
class RealSingleton implements Singleton {
    // public methods
}

// Used in unit tests
class FakeSingleton implements Singleton {
    // public methods
}

class ClientTest {
    private Singleton testSingleton = new FakeSingleton();
    @Test
    public void test() {
        Singleton.setSingleton(testSingleton);
        client.doSomething();
        // ...
    }
}
```
위 코드는 'Working Effectively With Legacy Code'책에서 "Introduce Static Setter"라고 불리는 방법이다.

인터페이스를 구현하면 다음과 같은 관점에서도 테스트하기 용이하다.

이런 코드 대신에
```java
void foo() {
   Bar bar = Bar.getInstance();
   // etc...
}
```
인터페이스를 구현하면 이렇게 쓸 수 있다.
```java
void foo(IBar bar) {
   // etc...
}
```
가짜 bar 객체를 통해서 foo 메소드를 테스트할 수 있게 된것이다.
다시 말하면, 의존성을 제거해서 bar를 테스트 하지 않고 foo를 테스트 할 수 있게 된 것이다.

이제 싱글톤 방식에는 어떠한 것들이 있는지 알아보자.

## 1️⃣ public static final 필드 방식의 싱글톤  

```java
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();
    private Elvis() {}
    
    public void leaveTheBuilding() { ... }
}
```
리플렉션 API인 AccessibleObject.setAccessible을 사용해 private 생성자를 호출할 수도 있다.
이러한 공격을 방어하려면 생성자를 수정하여 두 번째 객체가 생성되려 할 때 예외를 던지게 하자.

public static final 필드 방식의 장점은 클래스가 싱글톤임이 API에 명백히 드러난다는 것이다.

## 2️⃣ 정적 팩터리 방식의 싱글톤
```java
public class Elvis {
    private static final Elvis INSTANCE = new Elvis();
    private Elvis() {}
    public static Elvis getInstance() { return INSTANCE; }
    
    public void leaveTheBuilding() { ... }
}
```
정적 팩터리 방식의 장점은 크게 3가지이다.
### 1. API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있다.  
유일한 인스턴슬르 반환하던 팩터리 메서드가 (예컨대) 호출하는 스레드별로 다른 인스턴스를 넘겨주게 할 수 있다.

### 2. 원한다면 정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있다.
### 3. 메서드 참조를 공급자(supplier)로 사용할 수 있다.
가령 Elvis::getInstance를 Supplier<Elvis>로 사용하는 식이다.

## 3️⃣ 위에 언급한 2가지 방식의 싱글턴을 직렬화 할때는 주의하자

모든 인스턴스 필드를 일시적(transient)이라고 선언하고 readResolve 메소드를 제공해야한다.  
이렇게 하지 않으면 직렬화된 인스턴스를 역직렬화할 때마다 새로운 인스턴스가 만들어진다.

## 4️⃣ 열거 타입 방식의 싱글톤 (바람직한 방법)
```java
public enum  Elvis {
    INSTANCE;

    public void leaveTheBuilding() { ... }
}
```
간결하고 추가 노력 없이 직렬화할 수 있다.  
심지어 아주 복잡한 직렬화 상황이나 리플렉션 공격에서도 제2의 인스턴스가 생기는 일을 완벽히 막아준다.  
**대부분 상황에서는 원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법이다**

단, 만들려는 싱글턴이 Enum 외의 클래스를 상속해야 한다면 이 방법은 사용할 수 없다.
(열거 타입이 다른 인터페이스를 구현하도록 선언할 수는 있다.)
___
참고(Reference)  
[mocking-a-singleton-class](https://stackoverflow.com/questions/2302179/mocking-a-singleton-class)  
[advantage-of-singleton-implementing-an-interface](https://stackoverflow.com/questions/17988251/advantage-of-singleton-implementing-an-interface)  
[why-doesnt-mockito-mock-static-methods](https://stackoverflow.com/questions/4482315/why-doesnt-mockito-mock-static-methods)  
[static method와 Override hiding 대한 정리](https://wedul.site/457)  
[static method를 mocking 하기](https://roybatty.tistory.com/11)  
[스태틱 클래스 테스트하기](https://sungminoh.github.io/posts/development/java-mock-static-method/)
