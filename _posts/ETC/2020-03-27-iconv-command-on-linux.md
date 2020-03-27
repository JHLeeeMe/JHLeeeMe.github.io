---
date: 2020-03-27 08:14:07
layout: post
title: "Linux의 iconv 명령어 (파일 인코딩 변경)"
subtitle:
description:
image:
optimized_image:
category: ETC
tags:
  - ETC
author: JHLeeeMe
paginate: false
---

본인의 OS는 Ubuntu18.04 이다.  
Windows의 파일을 Ubuntu에서 열면 폰트가 깨져서 보인다.  
파일 인코딩이 ```euc-kr```이기 때문이다.  
밑에 예제는 일부러 반대로 해봤다.

먼저 vim으로 test.txt이라는 텍스트 파일을 만들고
![0](https://user-images.githubusercontent.com/31606119/77736334-6ce69700-704f-11ea-81a5-a611c01219b4.png)

```utf-8```로 만들어 졌을 것이다.  
확인 해보자.
## 파일 인코딩 확인
```$ file -bi [파일이름]```
![1](https://user-images.githubusercontent.com/31606119/77736339-6eb05a80-704f-11ea-878c-9bf4dc0d46c2.png)

## utf-8 to euc-kr(iso-8859-1)
![2](https://user-images.githubusercontent.com/31606119/77736344-6f48f100-704f-11ea-9a60-4b12ac046c4b.png)
잘 바뀌엇다.  
파일을 열어보자.
![3](https://user-images.githubusercontent.com/31606119/77736348-6fe18780-704f-11ea-9bd3-53f7588505db.png)
예상대로 깨져보인다.
