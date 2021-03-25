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

title: "Effective Java Item 08"
excerpt: "finalizer와 cleaner 사용을 피하라"
toc: true
toc_sticky: true
toc_label: "목차"
categories:
  - Effective Java
tags:
  - [Effective Java, finalizer, cleaner]
date: 2021-03-20
last_modified_at: 2021-03-20
---
# finalizer와 cleaner 사용을 피하라

## finalizer란?
객체가 소멸될 때 호출하기로 약속된 메소드


***
참고(Reference)  
[Finalizer Attack in Java](https://self-learning-java-tutorial.blogspot.com/2020/03/finalizer-attack-in-java.html)