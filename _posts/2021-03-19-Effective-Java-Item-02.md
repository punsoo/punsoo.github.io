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

title: "Effective Java Item 02"
excerpt: "생성자에 매개변수가 많다면 빌더를 고려하라"
toc: true
toc_sticky: true
toc_label: "목차"
categories:
  - Effective Java
tags:
  - [Effective Java, 빌더 패턴]
date: 2021-03-19
last_modified_at: 2021-03-19
---

# 생성자에 매개변수가 많다면 빌더를 고려하라

## 1️⃣ 점층적 생성자 패턴은 매개변수가 많아지면 클라이언트 코드를 작성하거나 읽기 어렵다.  

점층적 생성자 패턴에서는 사용자가 설정하길 원치 않는 매개변수까지 포함하기 쉽다.  
또한, 코드를 읽을 때 각 값의 의미가 무엇인지 헷갈릴 것이고, 매개변수가 몇 개인지도 주의해서 세어보아야 할 것이다. 
타입이 같은 매개변수가 연달아 늘어서 있으면 찾기 어려운 버그로 이어질 수 있다. 클라이언트가 매개변수의 순서를 
바꿔 건네줘도 컴파일러는 알아채지 못하고, 결국 런타임에 엉뚱한 동작을 하게 된다.

## 2️⃣ 자바빈즈 패턴도 대안이 될 수 없다.

매개변수가 없는 생성자로 객체를 만든 후, 세터 메서드들을 호출해 원하는 매개변수의 값을 설정하는 방식이다. 
하지만 객체 하나를 만들려면 메서드를 여러 개 호출해야 하고, 객체가 완전히 생성되기 전까지는 일관성(Consistency)이 
무너진 상태에 놓이게 된다.

*일관성이 무너진 상태란?*
*객체가 완전히 생성되기 전 초기화가 덜 된 상태를 말한다.  
사용자가 실수로라도 완전히 생성되기 전의 객체를 사용해버리면 런타임 에러가 발생할 수 있다.*

점층적 생성자 패턴에서는 매개변수들이 유효한지를 생성자에서만 확인하면 일관성을 유지할 수 있었는데, 
그 장치가 완전히 사라진 것이다. 일관성이 깨진 객체가 만들어지면, 버그를 심은 코드와 그 버그 때문에 런타임에 
문제를 겪는 코드가 물리적으로 멀리 떨어져 있을 것이므로 디버깅도 만만치 않다.

*물리적으로 멀리 떨어진 코드란?*  
*여기서 물리적으로 떨어진 두 대상은 "버그의 원인이 되는 코드"와 "버그가 발생한 코드"의 위치이다.  
런타임 에러 코드가 발생되는 곳은 초기화를 하는 코드부분이 아니라, 불완전한 객체를 넘겨받아서 그 객체를 구동하는 
코드이다. 이 둘의 코드는 전혀 다른 클래스, 혹은 심지어 전혀 다른 패키지 일 수도 있으므로 코드가 물리적으로 멀리 
떨어져 있다고 하는 것이다.*

일관성이 무너지는 문제 때문에 자바빈즈 패턴에서는 클래스를 불변으로 만들 수 없으며 스레드 안정성을 얻으려면 프로그래머가 
추가 작업(ex freezing)을 해줘야만 한다.

## 3️⃣ 안정성과 가독성을 겸비한 빌더 패턴이라는 대안이 있다.

점층적 생성자 패턴의 안전성과 자바빈즈 패턴의 가독성을 겸비한 빌더패턴이다.  
클라이언트는 필요한 객체를 직접 만드는 대신, 필수 매개변수만으로 생성자(혹은 정적 팩터리)를 호출해 빌더 객체를 얻는다. 
그 다음 빌더 객체가 제공하는 일종의 세터 메소드들로 원하는 선택 매개변수들을 설정한다.  
마지막으로 매개변수가 없는 build 메소드를 호출해 드디어 우리에게 필요한 (보통은 불변인) 객체를 얻는다.  
빌더는 생성할 클래스 안에 정적 멤버 클래스로 만들어두는 게 보통이다.

``` java
public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;

    public static class Builder {
        // 필수 매개변수
        private final int servingSize;
        private final int servings;

        // 선택 매개변수 - 기본값으로 초기화한다.
        private int calories = 0;
        private int fat = 0;
        private int sodium = 0;
        private int carbohydrate = 0;

        // 필수 매개변수만을 담은 Builder 생성자
        public Builder(int servingSize, int servings) {
            this.servingSize = servingSize;
            this.servings = servings;
        }

        // 선택 매개변수의 setter, Builder 자신을 반환해 연쇄적으로 호출 가능
        public Builder calories(int val) 
            {  calories = val;         return this; }
        public Builder fat(int val)
            {  fat = val;         return this; }
        public Builder sodium(int val)
            {  sodium = val;         return this; }
        public Builder carbohydrate(int val)
            {  carbohydrate = val;         return this; }
        
        // build() 호출로 최종 불변 객체를 얻는다.
        public NutritionFacts build() {
            return new NutritionFacts(this);
        }
    }

    private NutritionFacts(Builder builder) {
        servingSize = builder.servingSize;
        servings = builder.servings;
        calories = builder.calories;
        fat = builder.fat;
        sodium = builder.sodium;
        carbohydrate = builder.carbohydrate;
    }
}
```
빌더 패턴을 적용한 위 NutritionFacts 클래스는 불변이며, 모든 매개변수의 기본값들을 한곳에 모아뒀다.  
빌더의 세터 메서드들은 빌더 자신을 반환하기 때문에 연쇄적으로 호출할 수 있다.  
다음은 이 클래스를 사용하는 클라이언트 코드의 모습이다.
```java
NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8)
        .calories(100).sodium(35).carbohydrate(27).build();
```
이 클라이언트 코드는 쓰기 쉽고, 무엇보다도 읽기 쉽다.  
빌더 패턴은 (파이썬과 스칼라에 있는) 명명된 선택적 매개변수(named optional parameters)를 흉내 낸 것이다.

## 4️⃣ 빌더 패턴은 계층적으로 설계된 클래스와 함께 쓰기에 좋다.

```java
public abstract class Pizza{
   public enum Topping { HAM, MUSHROOM, ONION, PEPPER, SAUSAGE }
   final Set<Topping> toppings;

   // 추상 클래스는 추상 Builder를 가진다. 서브 클래스에서 이를 구체 Builder로 구현한다.
   abstract static class Builder<T extends Builder<T>> {
      EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);
      public T addTopping(Topping topping) {
         toppings.add(Objects.requireNonNull(topping));
         return self();
      }

      abstract Pizza build();

      // 하위 클래스는 이 메서드를 overriding하여 this를 반환하도록 해야 한다.
      protected abstract T self();
   }

   Pizza(Builder<?> builder) {
      toppings = builder.toppings.clone();
   }
}

public class NyPizza extends Pizza {
   public enum Size { SMALL, MEDIUM, LARGE }
   private final Size size;

   public static class Builder extends Pizza.Builder<Builder> {
      private final Size size;

      public Builder(Size size) {
         this.size = Objects.requireNonNull(size);
      }

      @Override public NyPizza build() {
         return new NyPizza(this);
      }

      @Override protected Builder self() { return this; }
   }

   private NyPizza(Builder builder) {
      super(builder);
      size = builder.size;
   }
}

public class Calzone extends Pizza {
   private final boolean sauceInside;

   public static class Builder extends Pizza.Builder<Builder> {
      private boolean sauceInside = false;

      public Builder sauceInside() {
         sauceInside = true;
         return this;
      }

      @Override public Calzone build() {
         return new Calzone(this);
      }

      @Override protected Builder self() { return this; }
   }

   private Calzone(Builder builder) {
      super(builder);
      sauceInside = builder.sauceInside;
   }
}
```
추상 클래스는 추상 빌더를, 구체 클래스는 구체 빌더를 갖게 한다.  
Pizza.Builder 클래스는 재귀적 타입 한정을 이용하는 제네릭타입이다.  
여기에 추상 메서드인 self를 더해 하위 클래스에서는 형변환하지 않고도 메소드 연쇄를 지원할 수 있다.  
self 타입이 없는 자바를 위한 이 우회 방법을 시뮬레이트한 셀프 타입(simulated self-type) 관용구라 한다.

각 하위 클래스(NyPizza, Calzone)의 빌더가 정의한 build 메소드는 해당하는 구체 하위 클래스를 반환하도록 선언한다.  
하위 클래스의 메소드가 상위 클래스의 메소드가 정의한 반환 타입이 아닌, 그 하위 타입을 반환하는 기능을   
공변 반환 타이핑(covariant return typing)이라 한다.

## 5️⃣ 빌더 패턴의 장단점

빌더를 이용하면 가변인수(varargs) 매개변수를 여러 개 사용할 수 있다.
각각을 적절한 메서드로 나눠 선언하면 된다.
아니면 위 Pizza 코드에서의 addTopping 메소드처럼, 메소드를 여러 번 호출하도록 하고 각 호출 때 넘겨진 매개변수들을 하나의 
필드로 모을 수도 있다.  
빌더 패턴은 유연하다.  
빌더 하나로 여러 객체를 순회하면서 만들 수 있고, 빌더에 넘기는 매개변수에 따라 다른 객체를 만들 수도 있다.  
빌더 생성 비용이 크지는 않지만 성능에 민감한 상황에서는 문제가 될 수 있다.  
점층적 생성자 패턴보다는 코드가 장황해서 매개변수가 4개 이상은 되어야 값어치를 한다.  
하지만 API는 시간이 지날수록 매개변수가 많아지는 경향이 있으니 나중에 전환하기보다는 애초에 빌더 패턴으로 시작하는 편이 나을 때가 많다.