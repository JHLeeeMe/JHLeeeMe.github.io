---
date: 2020-03-25 10:36:25
layout: post
title: "Java '=='연산자와 equals() 메서드의 차이"
subtitle: "Java String Pool"
description:
image: https://user-images.githubusercontent.com/31606119/77540094-d9de1d80-6ee5-11ea-84ed-b4c057b643bf.png
optimized_image: https://user-images.githubusercontent.com/31606119/77540094-d9de1d80-6ee5-11ea-84ed-b4c057b643bf.png
category: Java
tags:
  - Java
author:
paginate: false
---

java에서 ```String``` 객체를 비교할 땐```equals()```메서드를 쓴다.  
```'=='``` 연산자와 ```equals()```의 차이를 알아볼 것이다.  
일단 그 전에 ```String Pool``` 이란 것을 먼저 알아보자!

---

## String Pool
* String 객체를 저장하기 위한 ```Heap Memory```의 특별한 공간
* ```String Pool```은 ```cache```이다.
* String 객체를 만드는 방법으로 ```Double Quotes```와 ```new 연산자``` 두 가지가 있다.
* 그 중 ```Double Quotes```로 객체를 생성하면 ```String Pool```에 저장된다.
* ```String Pool```에 이미 같은 값이 저장돼 있으면 새로운 String 객체를 생성하지 않고 같은 주소를 참조하도록 한다.  
덕분에 ```Memory```와 ```performance```에서 이점이 있다.
* ```new operator```로 객체를 생성하면 ```Heap Memory```에 저장된다.  
이 참조를 ```String pool```쪽으로 옮기고 싶다면 ```intern()``` 메서드를 쓸 수 있다.  

아래 코드는 String 객체를 만드는 두가지 방법을 모두 써서  만들어 봤다.

```java
public class JavaStringPool {
 
    public static void main(String[] args) {
        String s1 = "Hello";
        String s2 = "Hello";
        String s3 = new String("Hi");
        String s4 = "Hi";
 
        s3 = s3.intern();
    }
}
```
![java_string_pool](https://user-images.githubusercontent.com/31606119/77540094-d9de1d80-6ee5-11ea-84ed-b4c057b643bf.png)

---

### String 객체 생성시 new 연산자를 지양해야 할 이유
위에서 ```new 연산자```로 String 객체를 생성하면 ```Heap Memory```에 저장된다고 했다.  
생성되는 과정을 자세히 보자.     
0. ```String s3 = new String("Hi");```  
1. "Hi"라는 값의 문자열이 ```String Pool```에 생성된다.
2. 위의 단계에서 생성된 문자열을 전달하여 문자열 생성자를 호출한다.
3. ```Heap Memory```에 새 문자열이 생성되고 s3에 할당된다.
4. 처음 생성된, ```String Pool```에 있는 문자열은 쓸모없어져 ```가비지컬렉터```에 의해 수거될 것이다.
5. 우리는 ```Heap Memory```에 하나의 문자열을 만들려고 했지만 결국 두 개가 생성됨.

---

## '=='연산자와 equals()의 차이
호출엔 두 종류가 있다.
1. CBV(Call By Value), 값에 의한 호출  
=> ```primitive type``` (int, float, byte 등...)
2. CBR(Call By Reference), 참조 의한 호출  
=> ```reference type``` (Array, class 등...)

문자열은 ```reference type```이다.  
데이터의 주솟값을 참조해서 호출한다는 말이다.  
그리고 ```'=='``` 연산자는 주솟값을, ```equals()``` 메서드는 주소에 들어있는 데이터를 비교한다.  
그런데 위에서 봤듯이 문자열은 ```String Pool```이란 개념이 있어 데이터가 같아도 주소가 다를 수 있다.  
아래 코드를 보자.
```java
public class Test {

    public static void main(String[] args) {
        String s1 = "Hello";
        String s2 = "Hello";
        String s3 = new String("Hi");
        String s4 = "Hi";
        
        System.out.println("s1의 주솟값: " + System.identityHashCode(s1));
        System.out.println("s2의 주솟값: " + System.identityHashCode(s2));
        System.out.println("s3의 주솟값: " + System.identityHashCode(s3));
        System.out.println("s4의 주솟값: " + System.identityHashCode(s4));
        System.out.println(s1 == s2);
        System.out.println(s1.equals(s2));
        System.out.println(s3 == s4);
        System.out.println(s3.equals(s4));
        System.out.println("###########################");
        System.out.println();
        
        s3 = s3.intern();  // Heap Memory쪽으로 참조하던걸 String Pool쪽으로!
        System.out.println("s1의 주솟값: " + System.identityHashCode(s1));
        System.out.println("s2의 주솟값: " + System.identityHashCode(s2));
        System.out.println("s3의 주솟값: " + System.identityHashCode(s3));
        System.out.println("s4의 주솟값: " + System.identityHashCode(s4));
        System.out.println(s1 == s2);
        System.out.println(s1.equals(s2));
        System.out.println(s3 == s4);
        System.out.println(s3.equals(s4));
    }
}

------------------------------------------------------------------
Output:

s1의 주솟값: 705927765 (임의값)
s2의 주솟값: 705927765 (임의값)
s3의 주솟값: 366712642 (임의값)
s4의 주솟값: 1829164700 (임의값)
true
true
false
true
###########################

s1의 주솟값: 705927765 (임의값)
s2의 주솟값: 705927765 (임의값)
s3의 주솟값: 1829164700 (임의값)
s4의 주솟값: 1829164700 (임의값)
true
true
true
true
```
***s1*** => ```String Pool```에 ```705927765```라는 주솟값을 가지고 생성됐다.  

***s2*** => ```String Pool```에 이미 같은 데이터가 있으므로 그 데이터의 주솟값을 참조한다.  

***s3*** => ```String Pool```에 ```1829164700```라는 주솟값을 가지고 생성되었고, 문자열 생성자에 의해 ```Heap Memory```에도 ```366712642```라는 주솟값을 가지고 생성되었다.  
참조는 ```366712642``` 쪽이다.  

***s4*** => ```String Pool```에 이미 같은 데이터가 있으므로 그 데이터의 주솟값을 참조한다.

---

## 결론
reference type인 문자열은 ```주솟값```을 참조하는데...  
```String Pool```로 인해 ```주솟값```과 ```데이터 값```이 다를 수 있으므로 문자열의 데이터 값을 비교하고자 한다면 ```데이터 값```을 비교하는 메서드 ```equals()```를 쓰는게 안전하며.  
문자열 생성시에는 ```Double Quotes``` 방법을 지향하자...

---

## 참고 사이트
주소: [https://www.javastring.net](https://www.javastring.net)
