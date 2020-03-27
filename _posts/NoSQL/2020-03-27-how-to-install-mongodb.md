---
date: 2020-03-27 07:33:39
layout: post
title: "[MongoDB] Ubuntu에서 MongoDB 설치"
subtitle:
description:
image: https://user-images.githubusercontent.com/31606119/77732743-dadb9000-7048-11ea-8c66-b87d96b7f7d6.png
optimized_image: https://user-images.githubusercontent.com/31606119/77732743-dadb9000-7048-11ea-8c66-b87d96b7f7d6.png
category: NoSQL
tags:
  - NoSQL
  - mongoDB
author: JHLeeeMe
paginate: false
---

![MongoDB](https://user-images.githubusercontent.com/31606119/77732743-dadb9000-7048-11ea-8c66-b87d96b7f7d6.png)
Ubuntu18.04에서 MongoDB 4.2 설치하기  
MongoDB Documentation에서 그대로 가져온 내용  
버전마다 조금씩 다르니 자세한 건 아래 주소로  
주소: [https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/)

---

### 1. apt에서 사용할 공개 키(MongoDB 공개 GPG 키) 가져오기.
```bash
#!/usr/bin/env bash

wget -qO - https://www.mongodb.org/static/pgp/server-4.2.asc | sudo apt-key add -
```

### 2. Ubuntu 18.04(Bionic)용 목록 파일 생성
```bash
#!/usr/bin/env bash

echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.2.list
```

### 3. 패키지 데이터베이스 업데이트
```bash
#!/usr/bin/env bash

sudo apt update
```

### 4. MongoDB 설치
```bash
#!/usr/bin/env bash

sudo apt install -y mongodb-org
```

### 5. MongoDB 실행
```bash
#!/usr/bin/env bash

sudo service mongod start
```

### 6. 실행 확인
```netstat -na |grep LISTEN``` *OR* ```sudo service mongod status```
![result](https://user-images.githubusercontent.com/31606119/77732935-50476080-7049-11ea-9df0-22175ffda594.png)
