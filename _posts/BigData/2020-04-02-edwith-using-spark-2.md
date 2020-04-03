---
date: 2020-04-02 13:05:53
layout: post
title: "[Spark를 활용한 데이터분석] 2. 데이터 전처리"
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

# 데이터 전처리
-- 데이터 클렌징 이라고도 불림  
-- 데이터 전처리란 데이터를 가공해 분석에 용이하게 변경하는 과정  
-- 결측값 처리, 이상값 처리 등의 작업들을 말한다.

이전 강의에서 Load한 데이터를 클렌징할 작정이다.
### 이전 강의: [1. SparkSession 객체 생성 & 데이터 Load](https://jhleeeme.github.io/edwith-using-spark-1/)

---

## 전처리 작업목록
-- 필요없는 column 제거  
-- NA 문자열을 null로 바꾸기  
-- 몇몇 column들의 type 바꾸기  

위 과정을 처리하기 위해  
spark 기본 제공 함수가 아닌 유저가 직접 정의한 함수(UDF)를 사용할 예정이다.  
UDF(User Define Function)를 만들어보자.  

### 1. 일단 UDF함수로 변환할 함수 정의
```scala
#!/usr/bin/env scala


object UDFs {
    def stringToInteger(value: String): Option[Int] = {
        if ((value.isEmpty) || (value == "NA")) None
        else Some(value.toInt)
    }
    
    def integerToBoolean(value: Int): Boolean ={
        if (value == 0) false 
        else true
    }
}
```

### 2. udf함수로 변환
```scala
#!/usr/bin/env scala


import org.apache.spark.sql.functions.udf

val stringToIntegerFunction = udf(UDFs.stringToInteger _)
val integerToBooleanFunction = udf(UDFs.integerToBoolean _)
```

### 3. 데이터 처리
-- udf함수를 이용한 처리  
-- 처리한 데이터 프레임을 ```us_carrier_df```에 저장
```scala
#!/usr/bin/env scala


val us_carrier_df = raw_df
    .drop(
        // 사용하지 않을 column 삭제
        "DepTime", "CRSDepTime", "ArrTime", "CRSArrTime", "AirTime", "ArrDelay", "DepDelay", "TaxiIn", "TaxiOut",
        "CancellationCode", "CarrierDelay", "WeatherDelay", "NASDelay", "SecurityDelay", "LateAircraftDelay")
    .withColumn(
        // 'NA' to null & Integer type으로 변경
        "ActualElapsedTime", stringToIntegerFunction(raw_df("ActualElapsedTime")))
    .withColumn(
	// 'NA' to null & Integer type으로 변경
        "CRSElapsedTime", stringToIntegerFunction(raw_df("CRSElapsedTime")))
    .withColumn(
	// 'NA' to null & Integer type으로 변경
        "TailNum", stringToIntegerFunction(raw_df("TailNum")))
    .withColumn(
	// 'NA' to null & Integer type으로 변경
        "Distance", stringToIntegerFunction(raw_df("Distance")))
    .withColumn(
        // Boolean type으로 변경
        "Cancelled", integerToBooleanFunction(raw_df("Cancelled")))
    .withColumn(
	// Boolean type으로 변경
        "Diverted", integerToBooleanFunction(raw_df("Diverted")))
```

### 4. us_carrier_df 살펴보기
```scala
#!/usr/bin/env scala


// Schema 확인
us_carrier_df.printSchema()

// 실제 데이터 확인
us_carrier_df.show()

---------------------------------
Output:

root
 |-- Year: integer (nullable = true)
 |-- Month: integer (nullable = true)
 |-- DayofMonth: integer (nullable = true)
 |-- DayOfWeek: integer (nullable = true)
 |-- UniqueCarrier: string (nullable = true)
 |-- FlightNum: integer (nullable = true)
 |-- TailNum: integer (nullable = true)
 |-- ActualElapsedTime: integer (nullable = true)
 |-- CRSElapsedTime: integer (nullable = true)
 |-- Origin: string (nullable = true)
 |-- Dest: string (nullable = true)
 |-- Distance: integer (nullable = true)
 |-- Cancelled: boolean (nullable = true)
 |-- Diverted: boolean (nullable = true)

+----+-----+----------+---------+-------------+---------+-------+-----------------+--------------+------+----+--------+---------+--------+
|Year|Month|DayofMonth|DayOfWeek|UniqueCarrier|FlightNum|TailNum|ActualElapsedTime|CRSElapsedTime|Origin|Dest|Distance|Cancelled|Diverted|
+----+-----+----------+---------+-------------+---------+-------+-----------------+--------------+------+----+--------+---------+--------+
|1988|    1|         9|        6|           PI|      942|   null|               70|            64|   SYR| BWI|     273|    false|   false|
|1988|    1|        10|        7|           PI|      942|   null|               69|            64|   SYR| BWI|     273|    false|   false|
|1988|    1|        11|        1|           PI|      942|   null|               67|            64|   SYR| BWI|     273|    false|   false|
|1988|    1|        12|        2|           PI|      942|   null|               64|            64|   SYR| BWI|     273|    false|   false|
|1988|    1|        13|        3|           PI|      942|   null|               82|            64|   SYR| BWI|     273|    false|   false|
|1988|    1|        14|        4|           PI|      942|   null|               75|            64|   SYR| BWI|     273|    false|   false|
|1988|    1|        15|        5|           PI|      942|   null|               63|            64|   SYR| BWI|     273|    false|   false|
|1988|    1|        16|        6|           PI|      942|   null|               60|            64|   SYR| BWI|     273|    false|   false|
|1988|    1|        17|        7|           PI|      942|   null|               69|            64|   SYR| BWI|     273|    false|   false|
|1988|    1|        18|        1|           PI|      942|   null|               90|            64|   SYR| BWI|     273|    false|   false|
|1988|    1|        19|        2|           PI|      942|   null|               61|            64|   SYR| BWI|     273|    false|   false|
|1988|    1|        20|        3|           PI|      942|   null|               75|            64|   SYR| BWI|     273|    false|   false|
|1988|    1|        21|        4|           PI|      942|   null|               89|            64|   SYR| BWI|     273|    false|   false|
|1988|    1|        22|        5|           PI|      942|   null|               63|            64|   SYR| BWI|     273|    false|   false|
|1988|    1|        23|        6|           PI|      942|   null|               63|            64|   SYR| BWI|     273|    false|   false|
|1988|    1|        24|        7|           PI|      942|   null|               62|            64|   SYR| BWI|     273|    false|   false|
|1988|    1|        25|        1|           PI|      942|   null|               74|            64|   SYR| BWI|     273|    false|   false|
|1988|    1|        26|        2|           PI|      942|   null|               71|            64|   SYR| BWI|     273|    false|   false|
|1988|    1|        27|        3|           PI|      942|   null|               73|            64|   SYR| BWI|     273|    false|   false|
|1988|    1|        28|        4|           PI|      942|   null|               67|            64|   SYR| BWI|     273|    false|   false|
+----+-----+----------+---------+-------------+---------+-------+-----------------+--------------+------+----+--------+---------+--------+
only showing top 20 rows
```

### 5. us_carrier_df 캐싱
-- 자주 쓸 데이터이므로 클러스터 메모리상에 올리자.
```scala
#!/usr/bin/env scala


us_carrier_df.cache()
```

---

데이터 전처리도 끝났고,  
그 데이터를 us_carrier_df에 데이터프레임 형태로 저장도했고, 캐싱도 마쳤다.  
이제 us_carrier_df를 SQL문으로 톺아보자

### 1. '전역 임시 뷰' 생성
-- SQL의 VIEW  
-- ```createOrReplaceGlobalTempView()``` 메서드로 생성  
-- ```createOrReplaceTempView()``` 와의 차이점은 ```전역 임시 뷰```는 다른 세션에서도 사용할 수 있음.  
```scala
#!/usr/bin/env scala


us_carrier_df.createOrReplaceGlobalTempView("us_carrier")
```

### 2. SQL문으로 조회
-- ```전역 임시 뷰```는 시스템 데이터베이스에서 global_temp로 저장되므로 이를 참조하기 위해 전체 이름을 지정해야 함 (e.g. SELECT * FROM global_temp.view1)

```scala
#!/usr/bin/env scala


spark.sql("SELECT * FROM global_temp.us_carrier limit 10")

--------------------------------------------------
Output:

+----+-----+----------+---------+-------------+---------+-------+-----------------+--------------+------+----+--------+---------+--------+
|Year|Month|DayofMonth|DayOfWeek|UniqueCarrier|FlightNum|TailNum|ActualElapsedTime|CRSElapsedTime|Origin|Dest|Distance|Cancelled|Diverted|
+----+-----+----------+---------+-------------+---------+-------+-----------------+--------------+------+----+--------+---------+--------+
|1988|    1|         9|        6|           PI|      942|   null|               70|            64|   SYR| BWI|     273|    false|   false|
|1988|    1|        10|        7|           PI|      942|   null|               69|            64|   SYR| BWI|     273|    false|   false|
|1988|    1|        11|        1|           PI|      942|   null|               67|            64|   SYR| BWI|     273|    false|   false|
|1988|    1|        12|        2|           PI|      942|   null|               64|            64|   SYR| BWI|     273|    false|   false|
|1988|    1|        13|        3|           PI|      942|   null|               82|            64|   SYR| BWI|     273|    false|   false|
|1988|    1|        14|        4|           PI|      942|   null|               75|            64|   SYR| BWI|     273|    false|   false|
|1988|    1|        15|        5|           PI|      942|   null|               63|            64|   SYR| BWI|     273|    false|   false|
|1988|    1|        16|        6|           PI|      942|   null|               60|            64|   SYR| BWI|     273|    false|   false|
|1988|    1|        17|        7|           PI|      942|   null|               69|            64|   SYR| BWI|     273|    false|   false|
|1988|    1|        18|        1|           PI|      942|   null|               90|            64|   SYR| BWI|     273|    false|   false|
+----+-----+----------+---------+-------------+---------+-------+-----------------+--------------+------+----+--------+---------+--------+
```

다음 글 부터는 본격적으로 분석을 해보겠다.
### 다음 글: [2. 얼마나 많은 항공사가 있을까?](https://jhleeeme.github.io/edwith-using-spark-3/)

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


import org.apache.spark.sql.functions.udf

// 'UDFs' 라는 이름의 싱글턴 객체 안에
// udf로 변환할 메서드들 정의
object UDFs {                                                         
    def stringToInteger(value: String): Option[Int] = {               
        if ((value.isEmpty) || (value == "NA")) None                  
        else Some(value.toInt)                                        
    }                                                                 
                                                                      
    def integerToBoolean(value: Int): Boolean ={                      
        if (value == 0) false                                         
        else true                                                     
    }                                                                 
}

// udf 변환
val stringToIntegerFunction = udf(UDFs.stringToInteger _)
val integerToBooleanFunction = udf(UDFs.integerToBoolean _)

// udf을 활용한 데이터 처리
val us_carrier_df = raw_df
    .drop(
        // 사용하지 않을 column 삭제
        "DepTime", "CRSDepTime", "ArrTime", "CRSArrTime", "AirTime", "ArrDelay", "DepDelay", "TaxiIn", "TaxiOut",
        "CancellationCode", "CarrierDelay", "WeatherDelay", "NASDelay", "SecurityDelay", "LateAircraftDelay")
    .withColumn(
        // 'NA' to null & Integer type으로 변경
        "ActualElapsedTime", stringToIntegerFunction(raw_df("ActualElapsedTime")))
    .withColumn(
	// 'NA' to null & Integer type으로 변경
        "CRSElapsedTime", stringToIntegerFunction(raw_df("CRSElapsedTime")))
    .withColumn(
	// 'NA' to null & Integer type으로 변경
        "TailNum", stringToIntegerFunction(raw_df("TailNum")))
    .withColumn(
	// 'NA' to null & Integer type으로 변경
        "Distance", stringToIntegerFunction(raw_df("Distance")))
    .withColumn(
        // Boolean type으로 변경
        "Cancelled", integerToBooleanFunction(raw_df("Cancelled")))
    .withColumn(
	// Boolean type으로 변경
        "Diverted", integerToBooleanFunction(raw_df("Diverted")))

// Schema 확인
us_carrier_df.printSchema()

// 실제 데이터 확인
us_carrier_df.show()

// 캐싱
us_carrier_df.cache()

// 전역 임시 뷰 생성
us_carrier_df.createOrReplaceGlobalTempView("us_carrier")

// SQL문으로 조회
spark.sql("SELECT * FROM global_temp.us_carrier limit 10")
```

---

## Spark Doc 한글문서                                                 
[https://spark-korea.github.io/docs/](https://spark-korea.github.io/docs/)
