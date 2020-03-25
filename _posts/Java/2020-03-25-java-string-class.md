---
date: 2020-03-25 08:02:01
layout: post
title: "Java String Class"
subtitle: "문자열 다루기"
description:
image: https://user-images.githubusercontent.com/31606119/77519358-b190f780-6ec2-11ea-8726-737a73edbc03.png
optimized_image: https://user-images.githubusercontent.com/31606119/77519358-b190f780-6ec2-11ea-8726-737a73edbc03.png
category: Java
tags:
  - Java
author: JHLeeeMe
paginate: false
---

## Quick Overview
* Java String Class는 ```java.util``` 패키지에 담겨있다.
* 문자열 집합이며, ```primitive``` data type이 아닌 ```객체(Object)```이다.
* String 객체는 ```operator overloading```을 제공하는 유일한 class이다.  
(```'+'``` operator로 두 String 객체를 연결할 수 있다)
* String 객체는 ```immutable``` 이다. 
* String 객체들은 ```String Pool``` 이라는 heap space의 한 부분에 저장된다.
* String은 본질적으로 serializable하다. Serializable interface를 구현한다.
* 또 다른, String class에 의해 구현된 interface로 Comparable, CharSequence 등이 있다.  

---

## Create String
1. ***Double Quotes:***  
간단하고, 선호되는 방법. String 객체는 ```String Pool```에 저장된다.  
***e.g.*** ```String s1 = "Hello";```

2. ***new operator:***  
```Heap Memory```에 저장되며, String Pool의 이점을 취하지 못함.  
***e.g.*** ```String s2 = new String("Hello");```

![java_string_pool](https://user-images.githubusercontent.com/31606119/77519358-b190f780-6ec2-11ea-8726-737a73edbc03.png)

---

## Immutability의 이점
* 불변성에 의해  ```String Pool```이 가능하다. 즉, ```Memory``` 와 ```performance``` 의 이점을 가진다.
* 값을 바꿀 수 없으므로 더 안전하다.
* Thread Safety
* ```class loading``` 시에도 인수로 전달된 문자열 값을 변경할 수 없기때문에 안전하다.

## 단점
불변성에 의한 ```String Pool``` 사용으로 메모리와 퍼포먼스에서 이점을 가져가지만  
또한 그 불변성 때문에 String을 조작할 때 많은 메모리와 비효율적 운영을 야기한다.  
그래서, String을 조작을 위한 class로 ```StringBuffer```와 ```StringBuilder```가 존재한다.  
두 class들은 유사한데 ```StringBuffer```는 ```synchronized```하고  
```StringBuilder```는 그렇지 않다는게 차이가 있다.

---

## 중요한 Methods 몇가지
### 1. 문자열 연결
* ```<String> + <String>```
* ```concat()```

```java
String str0 = "Hello" + "World!";
String str1 = "Hello".concat("World!");

System.out.println(str0.equals(str1));

--------------------------------------
Output:

true
```

### 2. index로 문자 조회
* ```charAt()```

```java
char ch = "012345".charAt(4);

System.out.println(ch);

-------------------------------------
Output:

4
```

### 3. 문자로 index 조회 (charAt()과 반대)
* ```indexOf()```
* ```lastIndexOf()```

```java
int index0 = "Hello World".indexOf("l");
int index1 = "Hello World".lastindexOf("l");

System.out.println(index0);
System.out.println(index1);

---------------------------------------------
Output:

2
9
```

### 4. 문자 치환
* ```replace()```

```java
String str0 = "Hello World!".replace("l", "L");
String str1 = "Hello World!".replaceFirst("l", "L");

System.out.println(str0);
System.out.println(str1);

----------------------------------------------
Output:

HeLLo WorLd!
HeLlo World!
```

### 5. 여러 문자 치환
* ```substring()```

```java
String str = "01234556789".substring(2, 5);

System.out.println(str);

---------------------------------------------
Output:

234
```

### 6. 대, 소문자 변경
* ```toLowerCase()```
* ```toUpperCase()```

```java
String str0 = "Hello World!".toLowerCase();
String str1 = "Hello World!".toUpperCase();

System.out.println(str0);
System.out.println(str1);

--------------------------------------------
Output:

hello world!
HELLO WORLD!
```

### 7. 공백 제거
* ```trim()```

```java
String str = "     Hello World!          ".trim();

System.out.println(str);

--------------------------------------------------
Output:

Hello World!
```

### 8. 문자열 나누기
* ```split()```

```java
String[] str_array0 = "Hello World".split(" ");
String[] str_array1 = "Hello World".split("");

for (String str: str_array0) {
	System.out.println(str);
}

for (String str: str_array1) {
	System.out.println(str);
}

--------------------------------------------------
Output:

Hello
World
H
e
l
l
o
W
o
r
l
d
```

### 9. 시작, 끝 문자 확인
* ```startsWith()```
* ```endsWith()```

```java
String str = "Hello World!";

boolean isTrue0 = str.startsWith("Hell");
boolean isTrue1 = str.endsWith("orld!");

System.out.println(isTrue0);
System.out.println(isTrue1);
------------------------------------------
Output:

true
true
```

### 10. 문자열 비교
* ```equals()```

### 참고) Difference between '==' and equals()
```primitive type:``` ***int, float, double, byte, long 등...  
```CBV(Call By Value), 값에 의한 호출```  

```reference type:``` Array, class 등...  
```CBR(Call By Reference), 참조에 의한 호출```  

'=='는 주소값을 비교
```java
String a = "abc";
String b = "abc";
String c = new String("abc");

System.out.println(System.identityHashCode(a));
System.out.println(System.identityHashCode(b));
System.out.println(System.identityHashCode(c));

System.out.println(a.equals(b));
System.out.println(a.equals(c));
System.out.println(a==b);
System.out.println(a==c);

-----------------------------------------------
Output:

2001112025 (임의값)
2001112025 (임의값)
1919900000 (임의값)
true
true
true
false
```

### 11. 특정 문자열 포함관계 확인
* ```contains()```

```java
boolean isContain = "Hello World".contains("Worl");

System.out.println(siContain);

----------------------------------------------------
Output:

true
```

---
## 참고 사이트
주소: [https://www.javastring.net](https://www.javastring.net/)
