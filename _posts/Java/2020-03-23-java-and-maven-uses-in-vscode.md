---
date: 2020-03-23 01:30:56
layout: post
title: "vscode에서 java, maven 개발환경 설정하기"
subtitle:
description:
image: https://user-images.githubusercontent.com/31606119/77272593-efd8bc00-6cf4-11ea-91f7-be58df444d5a.png
optimized_image: https://user-images.githubusercontent.com/31606119/77272593-efd8bc00-6cf4-11ea-91f7-be58df444d5a.png
category: Java
tags:
  - Java
  - vscode
  - maven
  - IDE
author: JHLeeeMe
paginate: false
---

약 2년 반 전에 학원에서  
java를 이용한 빅데이터 분석 및 응용소프트웨어 교육과정을 들엇다.  
그 뒤로는 python만 다뤄서  
오랜만에 java 문법부터 다시 공부하기 위해 개발환경을 설정하고 있다.  
학원에서는 eclipse로 배웠지만 쓰면서도 무겁다는 느낌을 계속 받았다.  
그래서 이번에는 vscode를 써보기로 했다.  
OS는 **Ubuntu18.04** 버전이다.  

---

# 시나리오
1. Java 설치
2. Maven 설치
3. vscode 설치
4. Java 확장기능 설치 **on vscode**
5. Create Maven Project!

---

## 1. Java 설치
java 13 까지 나와있었지만 13.0.1 버전이라... java 11 버전(11.0.5)으로 받았다.  
주소: [Oracle Java Downloads](https://www.oracle.com/technetwork/java/javase/downloads/index.html)  

다운받은 tar.gz파일을 /opt 폴더에 풀고,  
```$ sudo tar xzvf jdk-11.0.5_linux-x64_bin.tar.gz -C /opt```
<a href='https://user-images.githubusercontent.com/31606119/77272581-ecddcb80-6cf4-11ea-9bee-a8d863a624a1.png'>
![0](https://user-images.githubusercontent.com/31606119/77272581-ecddcb80-6cf4-11ea-9bee-a8d863a624a1.png)
</a>

```.bashrc``` or ```.zshrc```파일에 환경변수 설정을 해준다.
<a href='https://user-images.githubusercontent.com/31606119/77272583-ed766200-6cf4-11ea-98c9-9522ae7bc387.png'>
![1](https://user-images.githubusercontent.com/31606119/77272583-ed766200-6cf4-11ea-98c9-9522ae7bc387.png)
</a>

```$ source ~/.bashrc``` 명령줄로 바뀐 내용 최신화 시켜준 뒤  
버전 확인으로 환경변수가 잘 설정됐는지 확인해보자.
<a href='https://user-images.githubusercontent.com/31606119/77272584-ee0ef880-6cf4-11ea-86a9-ffe7c72a001b.png'>
![2](https://user-images.githubusercontent.com/31606119/77272584-ee0ef880-6cf4-11ea-86a9-ffe7c72a001b.png)
</a>


## 2. Maven 설치
주소: [Apache Maven Downloads](https://maven.apache.org/download.cgi)  
mirror를 naver로 바꿔 조금이라도 빠르게 다운받자.
<a href='https://user-images.githubusercontent.com/31606119/77272586-eea78f00-6cf4-11ea-8676-70cf19cab436.png'>
![3](https://user-images.githubusercontent.com/31606119/77272586-eea78f00-6cf4-11ea-8676-70cf19cab436.png)
</a>

java와 마찬가지로 /opt 폴더에 풀고, ```.bashrc``` 에 환경변수 설정을 해주자.
<a href='https://user-images.githubusercontent.com/31606119/77272589-ef402580-6cf4-11ea-8997-c39ea76a61fd.png'>
![4](https://user-images.githubusercontent.com/31606119/77272589-ef402580-6cf4-11ea-8997-c39ea76a61fd.png)
</a>

버전 확인도
<a href='https://user-images.githubusercontent.com/31606119/77272591-ef402580-6cf4-11ea-8d26-3a3680637d0c.png'>
![5](https://user-images.githubusercontent.com/31606119/77272591-ef402580-6cf4-11ea-8d26-3a3680637d0c.png)
</a>


## 3. vscode 설치
본인은 우분투 소프트웨어 앱으로 다운로드하였다. 인터넷에서도 받을 수 있다.  
주소: [Visual Studio Code](https://code.visualstudio.com)  


## 4. Java 확장기능 설치 **on vscode**
EXTENSIONS 탭(좌측 사이드바에서 5번째 그림)에서 java를 검색하고,  
Java Extension Pack을 설치하자.
<a href='https://user-images.githubusercontent.com/31606119/77272593-efd8bc00-6cf4-11ea-91f7-be58df444d5a.png'>
![6](https://user-images.githubusercontent.com/31606119/77272593-efd8bc00-6cf4-11ea-91f7-be58df444d5a.png)
</a>

아래와 같은 java관련 확장 기능들이 모여있는 팩이다.
<a href='https://user-images.githubusercontent.com/31606119/77272596-f109e900-6cf4-11ea-8a0a-6486868fc19e.png'>
![7](https://user-images.githubusercontent.com/31606119/77272596-f109e900-6cf4-11ea-8a0a-6486868fc19e.png)
</a>

***(!! 아래 설정은 위에서 zshrc에 환경변수를 설정했으므로 안해줘도 된다!)***  
확장기능 인스톨 후, file 탭> Preferences > settings를 누르고,  
***java.home***을 치고 ```Edit in settings.json``` 을 클릭해 설정 파일을 켜보자.
<a href='https://user-images.githubusercontent.com/31606119/77272597-f109e900-6cf4-11ea-8092-948d53ea1491.png'>
![8](https://user-images.githubusercontent.com/31606119/77272597-f109e900-6cf4-11ea-8092-948d53ea1491.png)
</a>

초기 세팅 파일과 다르지만 중간에 ```"java.home":"/opt/jdk-11.0.5"``` 가 보인다. 써넣자.
<a href='https://user-images.githubusercontent.com/31606119/77272598-f1a27f80-6cf4-11ea-80b0-013c5540c8c3.png'>
![9](https://user-images.githubusercontent.com/31606119/77272598-f1a27f80-6cf4-11ea-80b0-013c5540c8c3.png)
</a>


## 5. Create Maven Project!
우클릭 후 메이븐 프로젝트를 만들어보자.
<a href='https://user-images.githubusercontent.com/31606119/77272599-f23b1600-6cf4-11ea-8826-38610d4966f7.png'>
![10](https://user-images.githubusercontent.com/31606119/77272599-f23b1600-6cf4-11ea-8826-38610d4966f7.png)
</a>
![10](https://user-images.githubusercontent.com/31606119/77272599-f23b1600-6cf4-11ea-8826-38610d4966f7.png)

그러면 아래 사진처럼 archetype을 고르라는 창이 뜰 것이다. maven-archetype-quickstart를 눌러보자.
<a href='https://user-images.githubusercontent.com/31606119/77272601-f2d3ac80-6cf4-11ea-941a-64954e654b59.png'>
![11](https://user-images.githubusercontent.com/31606119/77272601-f2d3ac80-6cf4-11ea-941a-64954e654b59.png)
</a>

버전은 가장 최근(현재 1.4) 버전으로 선택하고,  
아래와 같이 터미널에서 groupID, 버전 등을 써넣으면!
<a href='https://user-images.githubusercontent.com/31606119/77272603-f36c4300-6cf4-11ea-87fa-efee37d3e52a.png'>
![12](https://user-images.githubusercontent.com/31606119/77272603-f36c4300-6cf4-11ea-87fa-efee37d3e52a.png)
</a>

프로젝트 하나가 만들어진다. (test01)
<a href='https://user-images.githubusercontent.com/31606119/77272604-f404d980-6cf4-11ea-9930-dbf2b74c547f.png'>
![13](https://user-images.githubusercontent.com/31606119/77272604-f404d980-6cf4-11ea-9930-dbf2b74c547f.png)
</a>

F5를 눌러 돌려보자.
<a href='https://user-images.githubusercontent.com/31606119/77272605-f404d980-6cf4-11ea-9cf5-97dc3c086e97.png'>
![14](https://user-images.githubusercontent.com/31606119/77272605-f404d980-6cf4-11ea-9cf5-97dc3c086e97.png)
</a>

터미널에 ```Hello World!``` 가 출력된다.  
다음에는  
메이븐 프로젝트를 *.jar 파일로 만들어보고 실행해보겠다.

---

## jar파일 생성 및 실행하기
내 글: [Maven으로 jar파일 생성 & 실행](https://jhleeeme.github.io/create-jar-with-maven-in-vscode)
