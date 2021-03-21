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

title: "Effective Java Item 04"
excerpt: "인스턴스화를 막으려거든 private 생성자를 사용하라"
toc: true
toc_sticky: true
toc_label: "목차"
categories:
  - Effective Java
tags:
  - [Effective Java, private 생성자]
date: 2021-03-20
last_modified_at: 2021-03-20
---

# 인스턴스화를 막으려거든 private 생성자를 사용하라  

정적 메서드와 정적 필드만을 담은 유틸리티 클래스를 만들고 싶을 때가 있다.  
*ex) java.lang.Math, java.util.Arrays, java.util.Collections, StringUtil, DateUtil*

정적 멤버만 담은 유틸리티 클래스는 인스턴스로 만들어 쓰려고 설계한 게 아니다.  
하지만 생성자를 명시하지 않으면 컴파일러가 자동으로 기본 생성자를 만들어준다.  
(매개변수를 받지 않는 public 생성자)  
추상 클래스로 만드는 것으로도 인스턴스화를 만들 수 없다.  
하위 클래스를 만들어 인스턴스화 할 수 있기 때문이다.

인스턴스화를 막는 방법은 private 생성자를 추가하는 것이다.

```java
public class StringUtill {
    // 기본 생성자가 만들어지는 것을 막는다(인스턴스화 방지용).
    private StringUtil(){
        throw new AssertionError();
    }
}
```
클래스 바깥에서 생성자에 접근할 수 없음은 물론, 클래스 내부에서라도 실수로 생성자를 호출하지 않도록 해준다.
이 방식은 상속을 불가능하게 해주는 효과도 있다.
