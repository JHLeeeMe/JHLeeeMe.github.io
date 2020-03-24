---
date: 2020-03-24 11:31:29
layout: post
title: "java의 자료구조 라이브러리 collections"
subtitle:
description:
image: https://user-images.githubusercontent.com/31606119/77422239-7c2bd180-6e10-11ea-8ec7-5096d4b8242e.png
optimized_image: https://user-images.githubusercontent.com/31606119/77422239-7c2bd180-6e10-11ea-8ec7-5096d4b8242e.png
category: Java
tags:
  - Java
author: JHLeeeMe
paginate: false
---

## Java Collections library
재사용 가능한 collection 데이터 구조를 구현하는 클래스 및 인터페이스 세트  
java.util 패키지에 담겨있다.
<a href='https://user-images.githubusercontent.com/31606119/77422239-7c2bd180-6e10-11ea-8ec7-5096d4b8242e.png'>
![0](https://user-images.githubusercontent.com/31606119/77422239-7c2bd180-6e10-11ea-8ec7-5096d4b8242e.png)
</a>

---

## Iterable<T> interface
```public interface Iterable<T>```  
java collections library의 root interface이다.  

### method
```java
- Iterator<T> Iterator()  

- default void forEach(Consumer<? super T> action)

- default Spliterator<T> spliterator()
```

### detail
docs: [https://docs.oracle.com/javase/8/docs/api/java/lang/Iterable.html](https://docs.oracle.com/javase/8/docs/api/java/lang/Iterable.html)

---

## Iterator interface
```public interface Iterator<E>```  
읽어오는 부분에서  
List 인터페이스 구현체들은 ```get()```,  
Queue 인터페이스 구현체들은 ```peek()```, ```poll()```, ```element()```을 사용하는데,  
Set은 중복 없는 집합을 나타내는 클래스로 배열과 같은 인덱스가 없어 읽는 방법이 다르다.  
처음부터 하나씩 읽어오는데 쓰는 메서드 ```next()```를 쓰며,  
다른 구현체에서도 자주 쓰이므로 Iterator 인터페이스를 만듣어서 담아두었다.  
```Iterable 인터페이스에 Iterator 인터페이스를 return하는 iterator()메서드를 둠.```

### method
```java
- boolean hasNext() 

- E next()

- default void remove()
```

### 구현 e.g.
```java
Iterator<String> iter = arrList.iterator();  // Iterator 인터페이스를 return 해주는 iterator() 메서드
while (iter.hasNext()) {
    String str = iter.next();
    System.out.println(str);
}
```

### detail
docs: [https://docs.oracle.com/javase/8/docs/api/java/util/Iterator.html](https://docs.oracle.com/javase/8/docs/api/java/util/Iterator.html)

---

## Collection<E> Interface
```public interface Collection<E> extends Iterable<E>```  

### method
```java
- boolean add(E e)

- void clear()

- void remove(Object o)

- boolean contains(Object o)

- boolean equals(Object o)

- Iterator<E> Iterator()

- int size()

- Object[] toArray()

- ... 그 외 여러 가지
```

### detail
docs: [https://docs.oracle.com/javase/8/docs/api/java/util/Collection.html](https://docs.oracle.com/javase/8/docs/api/java/util/Collection.html)

---

## List<E> Interface
```public interface List<E> extends Collection<E>```  

### method
```java
- E get(int index)

- E set(int index, E element)

- ... 그 외 여러 가지
```

### 구현 e.g.
```java
List<E> list1 = new ArrayList<E>();
List<E> list2 = new LinkedList<E>();
List<E> list3 = new Vector<E>();
List<E> list4 = new Stack<E>();
```

### detail
docs: [https://docs.oracle.com/javase/8/docs/api/java/util/List.html](https://docs.oracle.com/javase/8/docs/api/java/util/List.html)

---

## Queue<E> Interface
```public interface Queue<E> extends Collection<E>```  
먼저 들어온게 먼저 나간다.

### method
```java
- E poll()     // 조회 & 삭제, return element or null

- E peek()     // 조회, return element or null

- E element()  // 조회, return element or Throw NoSuchElementException
```

### 구현 e.g.
```java
Queue<E> q1 = new PriorityQueue<E>();
Queue<E> q2 = new ArrayDeque<E>();
```

### detail
docs: [https://docs.oracle.com/javase/8/docs/api/java/util/Queue.html](https://docs.oracle.com/javase/8/docs/api/java/util/Queue.html)

---

## Set<E> interface
```public interface Set<E> extends Collection<E>```  
중복x

### 구현 e.g.
```java
Set<E> s1 = new HashSet<E>();

Set<E> s2 = new LinkedHashSet<E>();

Set<E> s3 = new TreeSet<E>();
```

### detail
docs: [https://docs.oracle.com/javase/8/docs/api/java/util/Set.html](https://docs.oracle.com/javase/8/docs/api/java/util/Set.html)

---

List 인터페이스를 구현한 글래스 중  
Vector, Stack 클래스는 이제 거의 안 쓴다고 한다.  
참고: [https://stackoverflow.com/questions/1386275/why-is-java-vector-and-stack-class-considered-obsolete-or-deprecated](https://stackoverflow.com/questions/1386275/why-is-java-vector-and-stack-class-considered-obsolete-or-deprecated)
