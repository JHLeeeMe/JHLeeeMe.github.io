---
date: 2020-03-24 03:41:44
layout: post
title: "Maven으로 jar파일 생성 & 실행"
subtitle:
description:
image: https://user-images.githubusercontent.com/31606119/77388393-5207ee80-6dd3-11ea-8f8a-f2b99549741c.png
optimized_image: https://user-images.githubusercontent.com/31606119/77388393-5207ee80-6dd3-11ea-8f8a-f2b99549741c.png
category: Java
tags:
  - Java
  - maven
author: JHLeeeMe
paginate: false
---

OS는 Ubuntu18.04  
vscode상에서 진행한다.

---
# 시나리오
1. Maven Project 생성 & Run
2. jar파일 생성 & Run

---

## 1. Maven Project 생성 & Run
test라는 폴더 안에 Maven Project를 생성하고, 돌려보았다.  
- groupId: com.example
- artifactId: test01
- version: 0.0.1
- package: groupId와 동일하게

<a href="https://user-images.githubusercontent.com/31606119/77388390-503e2b00-6dd3-11ea-81ab-8b49459d8d25.png">
![0](https://user-images.githubusercontent.com/31606119/77388390-503e2b00-6dd3-11ea-81ab-8b49459d8d25.png)
</a>

## 2. jar파일 생성 & Run
```pom.xml``` 파일이 있는 디렉터리에서 jar파일을 생성한다.  
```$ mvn package (or mvn package -DskipTests)```
<a href="https://user-images.githubusercontent.com/31606119/77388392-516f5800-6dd3-11ea-8d53-8abdf6d41956.png">
![1](https://user-images.githubusercontent.com/31606119/77388392-516f5800-6dd3-11ea-8d53-8abdf6d41956.png)
</a>
<a href="https://user-images.githubusercontent.com/31606119/77388393-5207ee80-6dd3-11ea-8f8a-f2b99549741c.png">
![2](https://user-images.githubusercontent.com/31606119/77388393-5207ee80-6dd3-11ea-8f8a-f2b99549741c.png)
</a>

성공적으로 build되었다.  
```target``` 디렉터리가 추가되어 컴파일된 ```App.class``` 는  
```target/classes/{패키지명}/``` 안에 생성되고,  
```test01-0.0.1.jar``` 라는 파일이 생성된 걸 볼 수 있다.
<a href="https://user-images.githubusercontent.com/31606119/77388394-52a08500-6dd3-11ea-86b0-f6dc99579eb1.png">
![3](https://user-images.githubusercontent.com/31606119/77388394-52a08500-6dd3-11ea-86b0-f6dc99579eb1.png)
</a>

### jar파일을 실행 해보자.  
```$ java -jar [jar파일]```
<a href="https://user-images.githubusercontent.com/31606119/77388395-53391b80-6dd3-11ea-9f48-4f835e288d2b.png">
![4](https://user-images.githubusercontent.com/31606119/77388395-53391b80-6dd3-11ea-9f48-4f835e288d2b.png)
</a>

```manifest``` 에러가 난다.  
main 클래스를 찾지 못해서 발생하는 에러이다.  
```pom.xml``` 파일의 ```maven-jar-plugin``` 에서  
설정으로 ```<mainClass>[패키지명].[클래스명]</mainClass>``` 를 추가해주자.
<a href="https://user-images.githubusercontent.com/31606119/77388396-53391b80-6dd3-11ea-80aa-5f0c07001a96.png">
![5](https://user-images.githubusercontent.com/31606119/77388396-53391b80-6dd3-11ea-80aa-5f0c07001a96.png)
</a>

설정이 바뀌었으니  
만들었던 패키지를 삭제하고,  
```$ mvn clean```  
<a href="https://user-images.githubusercontent.com/31606119/77388397-53d1b200-6dd3-11ea-95e0-b490ea035223.png">
![6](https://user-images.githubusercontent.com/31606119/77388397-53d1b200-6dd3-11ea-95e0-b490ea035223.png)
</a>

다시 생성 및 실행  
```$ mvn package (or mvn package -DskipTests)```  
```$ java -jar target/test01-0.0.1.jar```
<a href="https://user-images.githubusercontent.com/31606119/77388398-546a4880-6dd3-11ea-825e-2d3b7f842ce8.png">
![7](https://user-images.githubusercontent.com/31606119/77388398-546a4880-6dd3-11ea-825e-2d3b7f842ce8.png)
</a>

이제 어디서든 실행 가능한 jar 파일을 만들었다.  
실행하면 ```Hello World!``` 를 출력해주는 프로그램이다.  
다른 디렉터리에서 실행시켜보자.
<a href="https://user-images.githubusercontent.com/31606119/77388400-546a4880-6dd3-11ea-87be-afc6777983d5.png">
![8](https://user-images.githubusercontent.com/31606119/77388400-546a4880-6dd3-11ea-87be-afc6777983d5.png)
</a>
