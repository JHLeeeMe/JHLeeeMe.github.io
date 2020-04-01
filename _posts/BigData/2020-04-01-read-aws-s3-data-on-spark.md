---
date: 2020-04-01 06:47:00
layout: post
title: "Spark에서 S3 데이터 읽어오기"
subtitle:
description:
image: https://user-images.githubusercontent.com/31606119/78124969-60957c00-744b-11ea-8d88-7d3a59d7f034.png
optimized_image: https://user-images.githubusercontent.com/31606119/78124969-60957c00-744b-11ea-8d88-7d3a59d7f034.png
category: BigData
tags:
  - BigData
  - Spark
  - AWS
author: JHLeeeMe
paginate: false
---

Spark는 ```Hadoop FileSystem API```를 통해 데이터를 읽고,  
AWS S3는 ```S3A FileSystem API```와 ```EMR FileSystemAPI```를 제공함.  
aws의 EMR 환경이 아니라면 모든 작업은 ```S3A FileSystem API```를 사용한다.  

본인은 EMR 환경이 아니며, local 환경이다.  
그러므로 ```S3A FileSystem API```를 사용해야 한다.  

일단 알겠고,  
이제부터 local 환경에서 aws S3 데이터를 읽는 방법을 알아보자.

---

# 준비물
1. 본인의 AWS계정 Access Key
2. Hadoop-AWS.jar
3. AWS-java.jar

### 1. 본인의 AWS계정 Access Key  
-- access key 생성방법은 아래 링크 참조  
내 글: [AWS Access Key 생성하기](https://jhleeeme.github.io/create-access-key-in-aws/)

### 2 & 3. Hadoop-AWS.jar, AWS-java.jar
-- ```Hadoop-AWS.jar```와 ```AWS-java.jar```는 아래 링크에서 다운받을 수 있다.  
링크: [MVN repository](https://mvnrepository.com/artifact/org.apache.hadoop/hadoop-aws)

본인의 Spark version은 2.2.2이며, Pre-built for Hadoop 2.7 이므로  
```Haddop-AWS.jar```는 가장 인기가 많은 ```2.7.3``` 버전을 다운받았으며, 
![0](https://user-images.githubusercontent.com/31606119/78109004-5d8e9180-7433-11ea-9d87-8cc72280dd98.png)

스크롤을 아래로 내려보면 관련 ```AWS-java.jar(version 1.7.4)```를 볼 수 있다.
![1](https://user-images.githubusercontent.com/31606119/78109007-5ebfbe80-7433-11ea-9484-6841659ba2ab.png)

세 가지가 모두 준비 됐다면 이제 Spark 설정을 해주자.

---

# Spark 설정
1. 다운받은 ```jar```파일을 ```$SPARK_HOME/jars/``` 디렉터리에 넣어준다.
2. Conf 수정  
-- Spark code 상에서 수정  
-- ```spark-defaults.conf``` 파일 수정

### 1. ```jar```파일 옮기기
```bash
#!/usr/bin/env bash

$ mv <path/to/your/jar> $SPARK_HOME/jars/
```
### 2. Conf 수정
-- Spark code 상에서 수정하는 방법은 아래와 같다.
```scala
#!/usr/bin/env scala

import org.apache.spark.SparkConf

val sConf = new SparkConf()

sConf.set("spark.hadoop.fs.s3a.impl", "org.apache.hadoop.fs.s3a.S3AFileSystem")
sConf.set("spark.hadoop.fs.s3a.access.key", "<본인의 access key ID>")
sConf.set("spark.hadoop.fs.s3a.secret.key", "<본인의 access key 패스워드>")
```
하지만 매번 SparkConf를 써 넣어주어야 하므로 그게 귀찮다면,  

-- ```spark-defaults.conf``` 파일 수정을 하자.  
```bash
#!/usr/bin/env bash

$ cd $SPARK_HOME/conf

# template이 존재하므로 복붙한다.
$ cp spark-defaults.conf.template spark-defaults.conf

$ vi spark-defaults.conf
    # 아래 내용 추가
    spark.hadoop.fs.s3a.impl        org.apache.hadoop.fs.s3a.S3AFileSystem
    spark.hadoop.fs.s3a.access.key  <본인의 access key ID>
    spark.hadoop.fs.s3a.secret.key  <본인의 access key 패스워드>
```
파일 수정이 완료됐다면 Spark를 재실행   
Zeppelin을 사용하고 있다면 마찬가지로 재실행

## 추가)
1번 단계에서 ```$SPARK_HOME/jars/``` 가 아닌 다른 경로에 jar파일을 저장했다면,  
설정으로 아래 내용을 추가해 주어야 한다.
```scala
// Code 상에서
sConf.setAll(List(("spark.driver.extraClassPath", "<your/hadoop-jar>:<your/java-jar>")))

// spark-defaults.conf
spark.driver.extraClassPath	<your/hadoop-jar>:<your/java-jar>
```
SparkConf.scala 참고함  
아래 그림은 SparkConf의 setAll() 메서드이다.
![2](https://user-images.githubusercontent.com/31606119/78120276-98e58c00-7444-11ea-9de8-5a65b194cfe5.png)
Github: [SparkConf.scala](https://github.com/apache/spark/blob/master/core/src/main/scala/org/apache/spark/SparkConf.scala)  

모든 설정은 끝났다.  
이제 S3에서 데이터를 읽어와보자.

---

# S3 데이터 읽어오기
-- edwith의 PySpark강좌에서 제공하는 S3 data를 읽어보겠다. 
```scala
#!/usr/bin/env scala

import org.apache.spark.sql.SparkSession

val spark = SparkSession.builder.appName("Spark with S3").getOrCreate()

// RDD를 DataFrame으로 바꾸는 것과 같은 암시적 변환(implicit conversion)을 처리하기 위해
import spark.implicit._

val df = spark.read.csv("s3a://edwith-pyspark-dataset")

df.printSchema()

--------------------------------------------------------
Output:

root
 |-- _c0: string (nullable = true)
 |-- _c1: string (nullable = true)
 |-- _c2: string (nullable = true)
 |-- _c3: string (nullable = true)
 |-- _c4: string (nullable = true)
 |-- _c5: string (nullable = true)
 |-- _c6: string (nullable = true)
 |-- _c7: string (nullable = true)
 |-- _c8: string (nullable = true)
 |-- _c9: string (nullable = true)
 |-- _c10: string (nullable = true)
 |-- _c11: string (nullable = true)
 |-- _c12: string (nullable = true)
 |-- _c13: string (nullable = true)
 |-- _c14: string (nullable = true)
 |-- _c15: string (nullable = true)
 |-- _c16: string (nullable = true)
 |-- _c17: string (nullable = true)
 |-- _c18: string (nullable = true)
 |-- _c19: string (nullable = true)
 |-- _c20: string (nullable = true)
 |-- _c21: string (nullable = true)
 |-- _c22: string (nullable = true)
 |-- _c23: string (nullable = true)
 |-- _c24: string (nullable = true)
 |-- _c25: string (nullable = true)
 |-- _c26: string (nullable = true)
 |-- _c27: string (nullable = true)
 |-- _c28: string (nullable = true)

df: org.apache.spark.sql.DataFrame = [_c0: string, _c1: string ... 27 more fields]
```
잘 읽어온다.

---

## 참고 사이트
Blog: [https://sungminoh.github.io/posts/development/use-s3-for-spark/](https://sungminoh.github.io/posts/development/use-s3-for-spark/)

Blog: [https://yahwang.github.io/posts/84](https://yahwang.github.io/posts/84)

Doc 한글문서: [https://spark-korea.github.io/docs/sql-getting-started.html](https://spark-korea.github.io/docs/sql-getting-started.html)

edwith 강좌: [pyspark을 활용한 데이터분석](https://www.edwith.org/sparktutorial)
