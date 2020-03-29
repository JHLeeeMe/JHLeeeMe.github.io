---
date: 2020-03-29 08:50:29
layout: post
title: "Spark & Zeppelin 설치하기"
subtitle:
description:
image: https://user-images.githubusercontent.com/31606119/77845481-3ee98a00-71ea-11ea-8f3e-ead4bc957564.png
optimized_image: https://user-images.githubusercontent.com/31606119/77845481-3ee98a00-71ea-11ea-8f3e-ead4bc957564.png
category: ETC
tags:
  - ETC
  - Spark
  - Zeppelin
author: JHLeeeMe
paginate: false
---

Spark을 공부할 작정으로 databricks에서 notebook환경을 구성했었다.  
사실 로컬에 깔았을 때 Zeppelin이 빌드가 잘 안돼서 클라우드 상에서 했었던 것...  
성격상 그냥 못넘어 가겠다. 찾아봤고 해결봤다.  
**버전차이 때문에 안됐었던 것.**  
**Zeppelin은 maven을 사용한 직접 빌드 방법으로 작성하겠다.**  
Spark은 ```standalone``` 모드로...  

### Version
Java: ```1.8.0```  
Saprk: ```2.2.2```  
Zeppelin: ```0.8.2```  
Maven: ```3.6.1```

---

# 시나리오
### 0. Java 다운로드 & 설치

### 1. Spark 다운로드 & 설치
- 1-1. Spark Conf 수정
- 1-2. 실행

### 2. Zeppelin 다운로드 & 설치
- 2-1. Zeppelin Conf 수정
- 2-2. 실행

---

## 0. Java 다운로드 & 설치
-- 다운로드 주소: [https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html](https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html)

-- 설치 & 환경변수 설정
```bash
#!/usr/bin/env bash

# /opt 디렉터리에 암축 해제
$ sudo tar xzvf jdk-8u241-linux-x64.tar.gz -C /opt/

# 현재 유저 홈디렉터리의 .bashrc 문서를 열어 아래 내용 추가(환경변수 설정)
$ sudo vi ~/.bashrc
  ########
  # JAVA #
  ########
  export JAVA_HOME=/opt/jdk-8u241-linux-x64
  export PATH=$JAVA_HOME/bin:$PATH
```

## 1. Spark 다운로드 & 설치
-- lastest Spark version(2.4.5)은 Zeppelin-0.8.2(lastest)과 호환x  # (2020년 2월 26일 기준)  
-- scala 2.12로 빌드 됐기 때문 (Zeppelin은 scala 2.10 | 2.11로 빌드)  
-- 버전을 찾아보니 2.2.x가 적당해 보였다.  
-- Spark-2.2.0 Doc을 보자.
<a href='https://user-images.githubusercontent.com/31606119/77845475-3bee9980-71ea-11ea-932a-e7ac7eba574c.png'>
![0](https://user-images.githubusercontent.com/31606119/77845475-3bee9980-71ea-11ea-932a-e7ac7eba574c.png)
</a>

-- 다운로드 주소: [https://archive.apache.org/dist/spark/spark-2.2.2/](https://archive.apache.org/dist/spark/spark-2.2.2/)  
-- 설치 & 환경변수 설정
```bash
#!/usr/bin/env bash

# /opt 디렉터리에 압축해제
$ sudo tar xzvf spark-2.2.2-bin-hadoop2.7.tgz -C /opt/

# chown 명령어를 통해 유저 소유로 바꿈. -R 옵션은 목적 디렉터리의 하위 디렉터리들까지 바꿔줌
$ sudo chown {user}.{user} -R /opt/spark-2.2.2-bin-hadoop2.7

# 환경변수 설정
$ sudo vi ~/.bashrc
    #########
    # SPARK #
    #########
    export SPARK_HOME=/opt/spark-2.2.2-bin-hadoop2.7
    export PATH=$SPARK_HOME/bin:$SPARK_HOME/sbin:$PATH

# source 명령어를 통한 .bashrc 적용
$ source ~/.bashrc
```

### 1.1 Spark Conf 수정
-- conf 디렉터리로 이동해보면 template들이 들어있다.
<a href='https://user-images.githubusercontent.com/31606119/77845476-3d1fc680-71ea-11ea-815e-2fc2c71ab81b.png'>
![1](https://user-images.githubusercontent.com/31606119/77845476-3d1fc680-71ea-11ea-815e-2fc2c71ab81b.png)
</a>

-- spark-env.sh.template을 복사해서 spark-env.sh라는 파일을 생성하자.
```bash
#!/usr/bin/env bash

# conf 디렉터리로 이동
$ cd /opt/spark-2.2.2-bin-hadoop2.7/conf

# template파일 복사, 생성
$ cd spark-env.sh.template spark-env.sh

# spark-env.sh 편집
$ vi spark-env.sh
```

-- 본인은 ```WORKER``` 를 3개, ```CORES per WORKER```를 1,  ```MEMORY```를 1GB로 설정했다.
<a href='https://user-images.githubusercontent.com/31606119/77845478-3db85d00-71ea-11ea-9065-a717e4295f80.png'>
![2](https://user-images.githubusercontent.com/31606119/77845478-3db85d00-71ea-11ea-9065-a717e4295f80.png)
</a>

### 1.2 실행 & 확인
```bash
#!/usr/bin/env bash

# master node 실행
$ start-master.sh

# worker node 실행
$ start-slaves.sh

# 확인
$ jps
```

-- ```spark-web-ui```를 통해 알아보자. ```localhost:8080```으로 들어가보면 이쁘게 나온다.
<a href='https://user-images.githubusercontent.com/31606119/77845479-3e50f380-71ea-11ea-9a41-4bd317ebe308.png'>
![3](https://user-images.githubusercontent.com/31606119/77845479-3e50f380-71ea-11ea-9a41-4bd317ebe308.png)
</a>

## Zeppelin 다운로드 & 설치
-- 다운로드 주소: [https://zeppelin.apache.org](https://zeppelin.apache.org)  
-- source download from git
```bash
#!/usr/bin/env bash

$cd /opt

# Zepplin source download from git
$ sudo git clone https://github.com/apache/zeppelin.git

# git download로 생성된 zeppelin 디렉터리를 {user}로 소유자 변경
$ sudo chown {user}.{user} -R zeppelin-0.8.2

# .bashrc 수정
$ vi ~/.bashrc
    ############
    # Zeppelin #
    ############
    export ZEPPELIN_HOME=/opt/zeppelin-0.8.2
    export PATH=$ZEPPELIN_HOME/bin:$PATH

# .bashrc 적용
$ source ~/.bashrc

# 이제 maven을 통해 build만 하면 될 줄 알았는데 에러가 난다. 
# 아래 명령어를 통해 scala를 2.11로 하자. (default 2.10)
# update all pom.xml to use scala 2.11
$ /opt/zeppelin/dev/change_scala_version.sh 2.11

# maven을 통한 build
$ cd /opt/zeppelin
$ mvn clean package -DskipTests -Pspark-2.2.2 -Pscala-2.11
```

### 2-1. Zeppelin Conf 수정
```bash
#!/usr/bin/env bash

# conf 디렉터리 이동
$ cd /opt/zeppelin-0.8.2/conf

# zeppelin-env.sh 복사 생성
$ cp zeppelin-env.sh.template zeppelin-env.sh

# 편집
$ vi zeppelin-env.sh
```

-- ```MASTER```주소는 아까 ```spark-web-ui```에 나와있다.  
-- ZEPPELIN_ADDR = {본인 IP | 0.0.0.0(전체허용) | customIP}  
-- ZEPPELIN_PORT = Spark가 8080을 쓰고 있으므로 본인은 ```7777```로 했다.
<a href='https://user-images.githubusercontent.com/31606119/77845480-3ee98a00-71ea-11ea-9fcb-4c4dc5705ce1.png'>
![4](https://user-images.githubusercontent.com/31606119/77845480-3ee98a00-71ea-11ea-9fcb-4c4dc5705ce1.png)
</a>

### 2.2 실행
```bash
#!/usr/bin/env bash

# 실행
$ zeppelin-daemon.sh start

# 확인
$ jps
```

-- 웹 클라이언트를 켜고 ```localhost:7777```을 들어가보자
<a href='https://user-images.githubusercontent.com/31606119/77845481-3ee98a00-71ea-11ea-8f3e-ead4bc957564.png'>
![5](https://user-images.githubusercontent.com/31606119/77845481-3ee98a00-71ea-11ea-8f3e-ead4bc957564.png)
</a>
<a href='https://user-images.githubusercontent.com/31606119/77845483-3f822080-71ea-11ea-8b43-7598c838b9f7.png'>
![6](https://user-images.githubusercontent.com/31606119/77845483-3f822080-71ea-11ea-8b43-7598c838b9f7.png)
</a>

---

## 참고 사이트
[https://zeppelin.apache.org/docs/0.8.2/setup/basics/how_to_build.html#how-to-build-zeppelin-from-source](https://zeppelin.apache.org/docs/0.8.2/setup/basics/how_to_build.html#how-to-build-zeppelin-from-source)

---

## 추가)
본인은 gcp의 컴퓨트 엔진에 환경을 구성하였다.  
local에서 Zeppelin을 들어가기 위해선 gcp 네트워크 설정에 7777포트를 열어주어야 한다.
<a href='https://user-images.githubusercontent.com/31606119/77845484-401ab700-71ea-11ea-9489-1e17cea5f52d.png'>
![7](https://user-images.githubusercontent.com/31606119/77845484-401ab700-71ea-11ea-9489-1e17cea5f52d.png)
</a>
<a href='https://user-images.githubusercontent.com/31606119/77845485-401ab700-71ea-11ea-9d4e-1e4d955cccee.png'>
![8](https://user-images.githubusercontent.com/31606119/77845485-401ab700-71ea-11ea-9d4e-1e4d955cccee.png)
</a>


