---
date: 2020-03-20 06:54:17
layout: post
title: "Ubuntu18.04에서 python pip로 mysqlclient설치 시 에러 해결방법"
subtitle: 
description:
image: https://user-images.githubusercontent.com/31606119/77141408-2a0d4800-6ac0-11ea-82c2-daa0a1e2e8ea.png
optimized_image: https://user-images.githubusercontent.com/31606119/77141408-2a0d4800-6ac0-11ea-82c2-daa0a1e2e8ea.png
category: ETC
tags:
  - ETC
  - error
  - mysqlclient
author: JHLeeeMe
paginate: false
---

Ubuntu18.04++ 환경에서  
pip install mysqlclient 에러

## 1. install dependencies package
```bash
$ sudo apt install libmysqlclient-dev gcc
```

## 2. 다시 pip install
```bash
$ pip install mysqlclient
```
