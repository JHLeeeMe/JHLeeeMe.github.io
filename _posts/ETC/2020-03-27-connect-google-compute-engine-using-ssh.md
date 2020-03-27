---
date: 2020-03-27 15:27:59
layout: post
title: "Google Compute Engine VM인스턴스에 SSH로 접속하기"
subtitle:
description:
image: https://user-images.githubusercontent.com/31606119/77775277-b22ab900-708f-11ea-82eb-4210ed4ed4cd.png
optimized_image: https://user-images.githubusercontent.com/31606119/77775277-b22ab900-708f-11ea-82eb-4210ed4ed4cd.png
category: ETC
tags:
  - ETC
  - Google
  - SSH
author: JHLeeeMe
paginate: false
---

Google Cloud Platform에서 Compute Engine을 통해 가상 머신을 만들었다.  
로컬 터미널에서 ssh로 접속해보자.

## 1. RSA키 생성
```bash
#!/usr/bin/env bash

$ ssh-keygen -t rsa -f ~/.ssh/[KEY_FILE_NAME] -C "[USERNAME]"
```
-- ```KEY_FILE_NAME```: 생성할 키 파일 이름  
-- ```USERNAME```: vm인스턴스를 생성한 Gmail, ```e.g. test@gmail.com```  

위 과정에서 생성 된 KEY는 ```~/.ssh``` 디렉터리 안에 생성되며,  
```[KEY_FILE_NAME]```, ```[KEY_FILE_NAME].pub``` 두 개의 파일이 생성됐을 것이다.

## 2. 공개 키(.pub)를 vm인스턴스에 등록
![0](https://user-images.githubusercontent.com/31606119/77775277-b22ab900-708f-11ea-82eb-4210ed4ed4cd.png)

## 3. SSH를 통해 접속해보자.
```bash
#!/usr/bin/env bash

$ ssh -i ~/.ssh/[KEY_FILE_NAME] [사용자 이름]@[vm인스턴스의 외부IP]
```
-- ```[사용자 이름]```: vm인스턴스를 생성한 Gmail, ```e.g. test```

---

## 추가) RSA키 없이 접속하기
위 과정을 통해 RSA키로 로컬터미널에서 ssh를 사용해 접속하는 법을 알았다.  
그런데 접속할 때마다 rsa키 파일을 지정하는게 귀찮다.  
rsa키 없이 접속하는 방법을 알아보자.

### 1. 일단 ssh 접속
```$ ssh -i ~/.ssh/[KEY_FILE_NAME] [사용자 이름]@[vm인스턴스의 외부IP]```

### 2. sshd_config 파일 수정
```$ vi /etc/ssh/sshd_config```
![1](https://user-images.githubusercontent.com/31606119/77775285-b35be600-708f-11ea-9ea8-cdff07175c08.png)
-- 위 그림과 같이 ```PasswordAuthentication```을 yes로 바꾸자.  
-- 비밀번호로 접속 가능하게 하는 옵션  
-- 다른 유저로 접속할 땐 비밀번호를 물어본다.

### 3. 접속
```$ ssh [사용자 이름]@[vm인스턴스의 외부IP]```  
이제 rsa키 없이 접속 가능
