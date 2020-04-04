---
date: 2020-03-24 05:48:17
layout: post
title: "Ubuntu18.04에서 eclipse 설치"
subtitle:
description:
image: https://user-images.githubusercontent.com/31606119/77393361-36571500-6de0-11ea-8294-455d698a49e4.png
optimized_image: https://user-images.githubusercontent.com/31606119/77393361-36571500-6de0-11ea-8294-455d698a49e4.png
category: ETC
tags:
  - ETC
  - eclipse
  - IDE
author: JHLeeeMe
paginate: false
---

코딩을 학원에서 처음 배웠다.  
java를 배웠는데 IDE로 eclipse를 썼엇다.  
다시 깔아보았는데 PATH문제가 있어서 정리해 보았다.  
물론 지금은 vscode를 쓰고잇다.  
java는 깔려있다고 생각하고 진행.

---

# 시나리오
1. eclipse 설치
2. eclipse.ini 설정 추가
3. eclipse 실행

---

## 1. eclipse 설치
주소: [Eclipse Packages](https://www.eclipse.org/downloads/packages/)

본인은 Ubuntu를 쓰고있으므로 linux버전으로 다운받았다.  
mirror를 한국서버로 바꾸자...  
그냥 다운받을려니깐 3시간이 걸린다고 나왔다...
<a href='https://user-images.githubusercontent.com/31606119/77393353-348d5180-6de0-11ea-8053-d4c8e1bb58a4.png'>
![0](https://user-images.githubusercontent.com/31606119/77393353-348d5180-6de0-11ea-8053-d4c8e1bb58a4.png)
</a>


## 2. eclipse.ini 설정 추가
다운받은 tar.gz파일을 ```/opt``` 디렉터리에 풀었고,  
```$ sudo tar xzvf eclipse-jee-2019-12-R-linux-gtk-x86_64.tar.gz -C /opt```  
eclipse를 실행시켜보았는데...  
에러가 난다. PATH가 안맞는단다.
<a href='https://user-images.githubusercontent.com/31606119/77393358-3525e800-6de0-11ea-9f1c-d2cc3fd0c350.png'>
![1](https://user-images.githubusercontent.com/31606119/77393358-3525e800-6de0-11ea-9f1c-d2cc3fd0c350.png)
</a>

```/opt```에 깔았으므로  
```eclipse.ini``` 파일을 열어 PATH설정을 해주자.
<a href='https://user-images.githubusercontent.com/31606119/77393360-35be7e80-6de0-11ea-9841-81e4a997746c.png'>
![2](https://user-images.githubusercontent.com/31606119/77393360-35be7e80-6de0-11ea-9841-81e4a997746c.png)
</a>


## 3. eclipse 실행
<a href='https://user-images.githubusercontent.com/31606119/77393361-36571500-6de0-11ea-8294-455d698a49e4.png'>
![3](https://user-images.githubusercontent.com/31606119/77393361-36571500-6de0-11ea-8294-455d698a49e4.png)
</a>
