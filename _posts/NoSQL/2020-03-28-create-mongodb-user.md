---
date: 2020-03-28 04:55:37
layout: post
title: "[MongoDB] mongodb 유저 생성하기"
subtitle:
description:
image: https://user-images.githubusercontent.com/31606119/77815330-4c751600-70fd-11ea-8284-7ecae648ea28.png
optimized_image: https://user-images.githubusercontent.com/31606119/77815330-4c751600-70fd-11ea-8284-7ecae648ea28.png
category: NoSQL
tags:
  - NoSQL
  - mongoDB
author: JHLeeeMe
paginate: false
---

![MongoDB](https://user-images.githubusercontent.com/31606119/77815330-4c751600-70fd-11ea-8284-7ecae648ea28.png)

NoSQL을 공부중이다.  
가장 인기가 많은 mongoDB를 선택했다.  
4.2 버전이다.  
Google Cloud Platform의 Compute Engine (like aws의 ec2)위에 설치했다.

---

## 1. mongo shell 실행 & 유저 생성
```bash
#!/usr/bin/env bash

$ mongo
(Non-default Port: $ mongo --port <port_number>)

mongo> use admin
mongo> db.createUser(
    {
        user: "<유저이름>",
        pwd: "<비밀번호>",
        roles: ["userAdminAnyDatabse",  # 
        "dbAdminAnyDatabase",           # 여러 역할들을 부여할 수 있다.
        "readWriteAnyDatabase"]         #
    }
)
```

## 2. mongod.conf 수정
```bash
#!/usr/bin/env bash

$ sudo vi /etc/mongo/mongod.conf

# 추가
security:
  authorization: enabled
```
![0](https://user-images.githubusercontent.com/31606119/77815309-1a63b400-70fd-11ea-888a-91e6399f6d1c.png)

## 3. mongod 재시작
```$ sudo service mongod restart```

## 4. 생성한 유저로 연결
-- 방법은 여러 가지 있다.  
-- password는 생략 가능, default(27017)는 생략가능  

### 방법 1.

```$ mongo --username <생성한 유저> --password --host <Host-IP> --port```  

### 방법 2.

```bash
$ mongo "mongodb://<생성한 유저>:<비밀번호>@<Host-IP>"```

if non-default post:
    $ mongo "mongodb://<생성한 유저>:<비밀번호>@<Host-IP>:<PORT>"
```

---

## 참고 사이트
주소: [https://docs.mongodb.com/manual/mongo/](https://docs.mongodb.com/manual/mongo/)
