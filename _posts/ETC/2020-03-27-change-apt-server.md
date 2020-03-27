---
date: 2020-03-27 07:02:00
layout: post
title: "Ubuntu apt서버를 daumkakao로 바꾸기"
subtitle:
description:
image: https://user-images.githubusercontent.com/31606119/77730702-a82f9880-7044-11ea-9201-b74af15a94bf.png
optimized_image: https://user-images.githubusercontent.com/31606119/77730702-a82f9880-7044-11ea-9201-b74af15a94bf.png
category: ETC
tags:
  - ETC
author:
paginate: false
---

본인이 UbuntuOS를 구축할 때마다 하는 작업이다.

## 1. 서버 확인
```$ sudo vi /etc/apt/sources.list```

## 2. vim 상에서 문자열 치환
- ```:%s/kr.archive.ubuntu.com/ftp.daumkakao.com/g```  
- ```:wq```

```%s``` -> 문서 전체에서 치환한다.  
```/kr.archive.ubuntu.com/ftp.daumkakao.com``` -> kr.archive.ubuntu.com을 ftp.daumkakao.com으로  
```/g``` -> 한 라인에서 패턴이 여러번 나오면 모두(지정하지 않으면 첫번째 패턴만)

## 결과화면
![0](https://user-images.githubusercontent.com/31606119/77730702-a82f9880-7044-11ea-9201-b74af15a94bf.png)
