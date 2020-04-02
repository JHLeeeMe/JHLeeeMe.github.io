---
date: 2020-04-01 12:02:17
layout: post
title: "[Spark를 활용한 데이터분석] 1. SparkSession 객체 생성 & 데이터 Load"
subtitle:
description:
image: https://user-images.githubusercontent.com/31606119/78127900-f29f8380-744f-11ea-8a9c-664efe56712f.jpeg
optimized_image: https://user-images.githubusercontent.com/31606119/78127900-f29f8380-744f-11ea-8a9c-664efe56712f.jpeg
category: BigData
tags:
  - BigData
  - Spark
author: JHLeeeMe
paginate: false
---

![Spark_대표이미지](https://user-images.githubusercontent.com/31606119/78127900-f29f8380-744f-11ea-8a9c-664efe56712f.jpeg)
### 강의 링크: [PySpark를 활용한 데이터분석](https://www.edwith.org/sparktutorial)
edwith의 PySpark를 활용한 데이터분석 강좌의 내용 정리.  

PySpark이 아닌 Scala Spark으로 진행

---

## 데이터 분석을 위한 준비 작업

먼저 local 환경에서 S3 data를 읽는 방법을 모른다면 아래 링크 참고.  
내 글: [Spark에서 S3 데이터 읽어오기](https://jhleeeme.github.io/read-aws-s3-data-on-spark/)

자 이제 강좌에서 제공하는 S3 저장소의 data를 읽어오자.   

### 1. SparkSession 객체 생성
-- SparkSession은 모든 Spark 작업의 시작점.  
```scala
#!/usr/bin/env scala

import org.apache.spark.sql.SparkSession

val spark = SparkSession
  .builder() 
  .appName("Spark EDA") 
  .getOrCreate()

// RDD를 DataFrame으로 바꾸는 것과 같은 암시적 변환(implicit conversion)을 처리하기 위해
import spark.implicits._
```

### 2. 데이터 Load
-- S3 저장소의 data 읽어오기
```scala
#!/usr/bin/env scala

val raw_df = spark.read
  .option("header", "true")
  .option("inferSchema", "true")
  .csv("s3a://edwith-pyspark-dataset")

// OR

val raw_df = spark.read
  .options(Map("header"-> "true", "inferSchema"-> "true"))
  .csv("s3a://edwith-pyspark-dataset")
```
데이터를 ```DataFrame```형태로 읽어들여 ```raw_df```에 저장했다.  
읽어들인 데이터를 간략하게 살펴보자.  

### 3. 데이터 살펴보기
이 작업에는 아래와 같은 메서드들을 사용할 수 있다.  
-- ```printSchema()```: schema 형태 표시  
-- ```count()```: data 갯수 표시  
-- ```describe()```: column 값의 분포 상태(갯수, 평균, 표준편차, 최소, 최대)를 포함하는 DataFrame 리턴  
-- ```show()```: 해당 dataFrame을 0번째 행부터 주어진 값 만큼 표시, default: 20개

먼저 schema를 확인해보자.
```scala
#!/usr/bin/env scala

raw_df.printSchema()

----------------------
Output:

root
 |-- Year: integer (nullable = true)
 |-- Month: integer (nullable = true)
 |-- DayofMonth: integer (nullable = true)
 |-- DayOfWeek: integer (nullable = true)
 |-- DepTime: string (nullable = true)
 |-- CRSDepTime: integer (nullable = true)
 |-- ArrTime: string (nullable = true)
 |-- CRSArrTime: integer (nullable = true)
 |-- UniqueCarrier: string (nullable = true)
 |-- FlightNum: integer (nullable = true)
 |-- TailNum: string (nullable = true)
 |-- ActualElapsedTime: string (nullable = true)
 |-- CRSElapsedTime: string (nullable = true)
 |-- AirTime: string (nullable = true)
 |-- ArrDelay: string (nullable = true)
 |-- DepDelay: string (nullable = true)
 |-- Origin: string (nullable = true)
 |-- Dest: string (nullable = true)
 |-- Distance: string (nullable = true)
 |-- TaxiIn: string (nullable = true)
 |-- TaxiOut: string (nullable = true)
 |-- Cancelled: integer (nullable = true)
 |-- CancellationCode: string (nullable = true)
 |-- Diverted: integer (nullable = true)
 |-- CarrierDelay: string (nullable = true)
 |-- WeatherDelay: string (nullable = true)
 |-- NASDelay: string (nullable = true)
 |-- SecurityDelay: string (nullable = true)
 |-- LateAircraftDelay: string (nullable = true)
```
-- column 수가 많다. -> ```describe()``` 메서드를 쓸 때 주의  
-- 의미상 정수형이나 시간형이어야 하는데 문자열이나 정수형으로 표시된 것들이 있다.  
-- 항공사(UniqueCarrier), 출발 공항(Origin), 도착 공항(Dest)은 문자열인데, 사전에 정해진 코드값을 쓰고 있다. -> 값의 갯수 자체는 그리 많지 않을 가능성 농후  
-- 취소 여부(Cancelled), 선회 여부(diverted)는 정수형이다. but 의미상 Boolean

이제 갯수를 보자.
```scala
#!/usr/bin/env scala

raw_df.count()

----------------
Output:

res24: Long = 123534969
```

대략 22년간의 데이터, 1억 2천만 행 정도 된다.  
실제로 어떻게 생겨먹었는지 확인해보자.
```scala
#!/usr/bin/env scala

raw_df.show()

---------------
Output:
￼
+----+-----+----------+---------+-------+----------+-------+----------+-------------+---------+-------+-----------------+--------------+-------+--------+--------+------+----+--------+------+-------+---------+----------------+--------+------------+------------+--------+-------------+-----------------+
|Year|Month|DayofMonth|DayOfWeek|DepTime|CRSDepTime|ArrTime|CRSArrTime|UniqueCarrier|FlightNum|TailNum|ActualElapsedTime|CRSElapsedTime|AirTime|ArrDelay|DepDelay|Origin|Dest|Distance|TaxiIn|TaxiOut|Cancelled|CancellationCode|Diverted|CarrierDelay|WeatherDelay|NASDelay|SecurityDelay|LateAircraftDelay|
+----+-----+----------+---------+-------+----------+-------+----------+-------------+---------+-------+-----------------+--------------+-------+--------+--------+------+----+--------+------+-------+---------+----------------+--------+------------+------------+--------+-------------+-----------------+
|1988|    1|         9|        6|   1348|      1331|   1458|      1435|           PI|      942|     NA|               70|            64|     NA|      23|      17|   SYR| BWI|     273|    NA|     NA|        0|              NA|       0|          NA|          NA|      NA|           NA|               NA|
|1988|    1|        10|        7|   1334|      1331|   1443|      1435|           PI|      942|     NA|               69|            64|     NA|       8|       3|   SYR| BWI|     273|    NA|     NA|        0|              NA|       0|          NA|          NA|      NA|           NA|               NA|
|1988|    1|        11|        1|   1446|      1331|   1553|      1435|           PI|      942|     NA|               67|            64|     NA|      78|      75|   SYR| BWI|     273|    NA|     NA|        0|              NA|       0|          NA|          NA|      NA|           NA|               NA|
|1988|    1|        12|        2|   1334|      1331|   1438|      1435|           PI|      942|     NA|               64|            64|     NA|       3|       3|   SYR| BWI|     273|    NA|     NA|        0|              NA|       0|          NA|          NA|      NA|           NA|               NA|
|1988|    1|        13|        3|   1341|      1331|   1503|      1435|           PI|      942|     NA|               82|            64|     NA|      28|      10|   SYR| BWI|     273|    NA|     NA|        0|              NA|       0|          NA|          NA|      NA|           NA|               NA|
|1988|    1|        14|        4|   1332|      1331|   1447|      1435|           PI|      942|     NA|               75|            64|     NA|      12|       1|   SYR| BWI|     273|    NA|     NA|        0|              NA|       0|          NA|          NA|      NA|           NA|               NA|
|1988|    1|        15|        5|   1331|      1331|   1434|      1435|           PI|      942|     NA|               63|            64|     NA|      -1|       0|   SYR| BWI|     273|    NA|     NA|        0|              NA|       0|          NA|          NA|      NA|           NA|               NA|
|1988|    1|        16|        6|   1327|      1331|   1427|      1435|           PI|      942|     NA|               60|            64|     NA|      -8|      -4|   SYR| BWI|     273|    NA|     NA|        0|              NA|       0|          NA|          NA|      NA|           NA|               NA|
|1988|    1|        17|        7|   1331|      1331|   1440|      1435|           PI|      942|     NA|               69|            64|     NA|       5|       0|   SYR| BWI|     273|    NA|     NA|        0|              NA|       0|          NA|          NA|      NA|           NA|               NA|
|1988|    1|        18|        1|   1349|      1331|   1519|      1435|           PI|      942|     NA|               90|            64|     NA|      44|      18|   SYR| BWI|     273|    NA|     NA|        0|              NA|       0|          NA|          NA|      NA|           NA|               NA|
|1988|    1|        19|        2|   1337|      1331|   1438|      1435|           PI|      942|     NA|               61|            64|     NA|       3|       6|   SYR| BWI|     273|    NA|     NA|        0|              NA|       0|          NA|          NA|      NA|           NA|               NA|
|1988|    1|        20|        3|   1337|      1331|   1452|      1435|           PI|      942|     NA|               75|            64|     NA|      17|       6|   SYR| BWI|     273|    NA|     NA|        0|              NA|       0|          NA|          NA|      NA|           NA|               NA|
|1988|    1|        21|        4|   1330|      1331|   1459|      1435|           PI|      942|     NA|               89|            64|     NA|      24|      -1|   SYR| BWI|     273|    NA|     NA|        0|              NA|       0|          NA|          NA|      NA|           NA|               NA|
|1988|    1|        22|        5|   1331|      1331|   1434|      1435|           PI|      942|     NA|               63|            64|     NA|      -1|       0|   SYR| BWI|     273|    NA|     NA|        0|              NA|       0|          NA|          NA|      NA|           NA|               NA|
|1988|    1|        23|        6|   1333|      1331|   1436|      1435|           PI|      942|     NA|               63|            64|     NA|       1|       2|   SYR| BWI|     273|    NA|     NA|        0|              NA|       0|          NA|          NA|      NA|           NA|               NA|
|1988|    1|        24|        7|   1328|      1331|   1430|      1435|           PI|      942|     NA|               62|            64|     NA|      -5|      -3|   SYR| BWI|     273|    NA|     NA|        0|              NA|       0|          NA|          NA|      NA|           NA|               NA|
|1988|    1|        25|        1|   1330|      1331|   1444|      1435|           PI|      942|     NA|               74|            64|     NA|       9|      -1|   SYR| BWI|     273|    NA|     NA|        0|              NA|       0|          NA|          NA|      NA|           NA|               NA|
|1988|    1|        26|        2|   1407|      1331|   1518|      1435|           PI|      942|     NA|               71|            64|     NA|      43|      36|   SYR| BWI|     273|    NA|     NA|        0|              NA|       0|          NA|          NA|      NA|           NA|               NA|
|1988|    1|        27|        3|   1332|      1331|   1445|      1435|           PI|      942|     NA|               73|            64|     NA|      10|       1|   SYR| BWI|     273|    NA|     NA|        0|              NA|       0|          NA|          NA|      NA|           NA|               NA|
|1988|    1|        28|        4|   1331|      1331|   1438|      1435|           PI|      942|     NA|               67|            64|     NA|       3|       0|   SYR| BWI|     273|    NA|     NA|        0|              NA|       0|          NA|          NA|      NA|           NA|               NA|
+----+-----+----------+---------+-------+----------+-------+----------+-------------+---------+-------+-----------------+--------------+-------+--------+--------+------+----+--------+------+-------+---------+----------------+--------+------------+------------+--------+-------------+-----------------+
only showing top 20 rows
```

Zeppelin 상에서 작업중이라면 ```z.show(raw_df)``` 을 통해 이쁘게 볼 수 있다.
![0](https://user-images.githubusercontent.com/31606119/78145073-63a06480-746b-11ea-87f6-78c5efc12bb1.png)

다음 글에서는  
이 데이터를 클렌징 해보겠다.
### 다음 글: [2. 데이터 전처리](https://jhleeeme.github.io/edwith-using-spark-2/)

---

## 전체 Code
```scala
#!/usr/bin/env scala

import org.apache.spark.sql.SparkSession

val spark = SparkSession
  .builder() 
  .appName("Spark EDA") 
  .getOrCreate()

// RDD를 DataFrame으로 바꾸는 것과 같은 암시적 변환(implicit conversion)을 처리하기 위해
import spark.implicits._

val raw_df = spark.read
  .option("header", "true")
  .option("inferSchema", "true")
  .csv("s3a://edwith-pyspark-dataset")

// OR
//
//val raw_df = spark.read
//  .options(Map("header"-> "true", "inferSchema"-> "true"))
//  .csv("s3a://edwith-pyspark-dataset")

//raw_df.printSchema()
//raw_df.count()
raw_df.show()
```

---

## Spark Doc 한글문서                                                
[https://spark-korea.github.io/docs/](https://spark-korea.github.io/docs/)
