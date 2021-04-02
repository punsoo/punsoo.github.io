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

title: "Effective Java Item 07"
excerpt: "다 쓴 객체 참조를 해제하라"
toc: true
toc_sticky: true
toc_label: "목차"
categories:
  - Effective Java
tags:
  - [Effective Java, 가비지 컬렉터]
date: 2021-03-24
last_modified_at: 2021-03-24
---

# 다 쓴 객체 참조를 해제하라

## 1️⃣ 객체 참조를 null 처리함으로 메모리 누수를 막을 수 있다.

게다가 null 처리한 참조를 사용하게 되면 프로그램은 즉시 NullpointerException을 던지며 종료된다.  
프로그램 오류는 가능한 한 조기에 발견하는게 좋다.

## 2️⃣ 객체 참조를 null 처리하는 일은 예외적인 경우여야 한다.

null 처리 남용은 프로그램을 필요 이상을 지저분하게 만들 뿐이다.  
다 쓴 참조를 해제하는 가장 좋은 방법은 그 참조를 담은 변수를 유효 범위 밖으로 밀어내는 것이다.  
변수의 범위를 최소가 되게 정의했다면 이 일은 자연스럽게 이뤄진다.

## 3️⃣ 자기 메모리를 직접 관리하는 클래스라면 메모리 누수에 주의하자.

원소를 다 사용한 즉시 그 원소가 참조한 객체들을 다 null 처리해줘야 한다.

## 4️⃣ 캐시를 사용할 때도 메모리 누수에 주의하자.

### 1. WeakHashMap을 사용해 캐시를 만들자

다 쓴 엔트리는 그 즉시 자동으로 제거될 것이다.

### 2. 쓰지 않는 엔트리를 주기적으로 청소해주자

백그라운드 스레드를 활용하거나 캐시에 새 엔트리를 추가할 때 부수 작업으로 수행하는 방법이 있다.  
LinkedHashMap은 removeEldestEntry 메서드를 써서 후자의 방식으로 처리한다.  
더 복잡한 캐시를 만들고 싶다면 java.lang.ref 패키지를 직접 활용하자.

## 5️⃣ 리스너 혹은 콜백을 사용할 때 메모리 누수에 주의하자.

클라이언트가 콜백을 등록만 하고 명확히 해지하지 않는다면 콜백은 계속 쌓여갈 것이다.  
이럴 때 콜백을 약한 참조로 저장하면 가비지 컬렉터가 즉시 수거해간다.  
예를 들어 WeakHashMap에 키로 저장하면 된다.