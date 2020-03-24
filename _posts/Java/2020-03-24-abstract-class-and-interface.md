---
date: 2020-03-24 06:51:15
layout: post
title: "추상 클래스(abstract class)와 인터페이스(interface)"
subtitle:
description:
image: https://user-images.githubusercontent.com/31606119/77402014-4a574280-6df1-11ea-9b0a-ac0e044298af.jpg
optimized_image: https://user-images.githubusercontent.com/31606119/77402014-4a574280-6df1-11ea-9b0a-ac0e044298af.jpg
category: Java
tags:
  - Java
author: JHLeeeMe
paginate: false
---

## 추상 클래스(abstract class)
```java
abstract class Calc {
    int a;  // 멤버 변수를 가질 수 있음
    int b;

    void add(int a, int b) {  // 일반 method
      System.out.println(a + b);
    }
  
    abstract void minus(int a, int b);  // method 앞에 abstract가 있으면 추상 메서드
}
```

## 인터페이스(interface)
아래 코드를 보자. 자바에서 말하는 인터페이스 표현이다.
```java
public abstract class Interface01 {
    public static final int NUM = 1;

    public abstract void add();
}
```
인터페이스는 자주 쓰인다.  
...  
귀찮다.  
interface를 만들어버렸다.
```java
public interface Interface01 {
    int NUM = 1;

    void add();
}
```
interface는  
```public한 인터페이스 상수``` (like 클래스 상수, static final)가 기본이다.  
```public한 추상 메서드``` 가 기본이다.

---

# OOP
프로그래밍의 설계 패러다임 중 하나로,  
현실 세계를 프로그램 설계에 반영한다는 개념을 기반으로 접근하는 방법.

OOP관점에서 class는 빵을 만드는 빵틀로 비유한다.  
즉, ```class(빵틀)```는 어떤 ```인스턴스(빵)```를 생성할 수 있다는 것인데,  
```추상 클래스```는 ```인스턴스```를 생성할 수 없다.  
실재가 없기 때문이다.  
그러니까  추상 클래스는 빵틀이 아니다.

여기 ```핸드폰```이 있고 ```노트북```이 있다.  
```핸드폰```과 ```노트북```은 공통으로 ```'on, off 기능'```이 있다.  
```핸드폰```과 ```노트북```은 전자제품이다.  
전자제품 클래스를 ```추상 클래스```로 만들고,  
```'on 메서드'와 'off 메서드'```를 정의한다.  
```'특이사항 메서드'```를 ```'추상 메서드'```로 만들어  
자식 클래스에서 정의를 강제할 수도 있다.  
전자제품은 관념으로만 존재한다.  
실재하는 건 핸드폰과 노트북이다.  
그러므로 java가 추구하는 설계 패러다임에 부합하다.

만약 ```'on, off기능'```만 있는 핸드폰과 노트북이 있다 하더라도  
전자제품 클래스는 ```추상 클래스``` 여야만 한다.  
그게 java의 근본이니깐.  
```추상 클래스```에 ```'추상 메서드'```가 없어도 되는 이유가 이것이라 생각된다.  


그럼 다시,  
전자제품이라는 관념을 꼭 추상화 시켜 클래스로 만들어야 하는 것인가?  
그렇다.  
상속, 기능의 확장. 편하잖아.  
개발에 이득. 생산성 증대.

즉, ```추상 클래스```를 써야하나 ```인터페이스```를 써야하나 고민하고, 헷갈릴게 아니라  
```추상 클래스```와 ```일반 클래스```를 고민해야 한다고 본다.  
```추상 클래스```와 ```인터페이스```는 목적에 따라 뭘 써야할지는 정해져있으니깐.

### 추상 클래스
- 일반적인 ```추상화``` 및 ```상속```에 더 초점을 맞춤

### 인터페이스
- 인터페이스 ```메서드의 구현을 강제``` 하는 것에 초점을 맞춘다고 보면 된다.

---

같은 것 같은데 다르다고 하니깐 헷갈렸다.  
개념적으로 접근부터 잘못됐다고 생각한다.  
둘은 같다고 결론 내렸다. 나만의 결론이다. 틀렸을 것이다. 아마도  
더 이상 알고 싶진 않다.
