---
date: 2020-04-05 11:01:55
layout: post
title: "[Spark를 활용한 데이터분석] 5. 운항 노선 알아보기"
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

## 키워드
-- DataFrame.```filter()```  
-- DataFrame.```count()```  
-- DataFrame.```distinct()```  
-- DataSet.```orderBy()```

이전 강의에 운항 거리와 운항 횟수를 알아봤다.   
이번엔 항공사들의 운항 노선을 알아보자.
### 이전 강의: [4. 항공사들의 운항거리, 횟수 톺아보기](https://jhleeeme.github.io/edwith-using-spark-4/)

---

## 1987년과 1993년의 운항 노선 수는?
-- 운항 노선이란 (출발지, 도착지)의 순서쌍  
-- 접근 절차  
ㄴ> 1. 원하는 년도의 운항 기록만 추출 (filter)  
ㄴ> 2. 1의 결과물에서 운항 노선((출발지, 도착지) 순서쌍이 중복되지 않는)만을 추출 (distinct)  
ㄴ> 3. 2의 결과물에 포함된 row 수 추출 (count)

### 1. 원하는 년도의 운항 기록 추출 (fliter)
-- ```select()``` 메서드로 필요한 Column (Origin, Dest)만 추출하자.`
```scala
#!/usr/bin/env scala


val us_carrier_1987_df = us_carrier_df.filter($"Year" === 1987).select($"Origin", $"Dest")
val us_carrier_1993_df = us_carrier_df.filter($"Year" === 1993).select($"Origin", $"Dest")
```

### 2. 1의 결과물에서 중복값 제거 (distinct)
-- DataFrame.```distinct()```메서드로 중복값 제거  
-- 살펴보기 쉽게 DataSet.```orderBy()``` 메서드로 정렬 후 출력
```scala
#!/usr/bin/env scala


// 중복값 제거
val us_carrier_1987_distinct_ds = us_carrier_1987_df.distinct()
val us_carrier_1993_distinct_ds = us_carrier_1993_df.distinct()

// 정렬 후 출력
us_carrier_1987_distinct_ds.orderBy($"Origin", $"Dest").show()
us_carrier_1993_distinct_ds.orderBy($"Origin", $"Dest").show()
-------------------------------------
Output:

us_carrier_1987_df: org.apache.spark.sql.DataFrame = [Origin: string, Dest: string]
us_carrier_1993_df: org.apache.spark.sql.DataFrame = [Origin: string, Dest: string]
us_carrier_1987_distinct_ds: org.apache.spark.sql.Dataset[org.apache.spark.sql.Row] = [Origin: string, Dest: string]
us_carrier_1993_distinct_ds: org.apache.spark.sql.Dataset[org.apache.spark.sql.Row] = [Origin: string, Dest: string]

+------+----+
|Origin|Dest|
+------+----+
|   ABE| ATL|
|   ABE| AVP|
|   ABE| DTW|
|   ABE| MDT|
|   ABE| ORD|
|   ABE| PHL|
|   ABE| PIT|
|   ABQ| AMA|
|   ABQ| DAL|
|   ABQ| DEN|
|   ABQ| DFW|
|   ABQ| ELP|
|   ABQ| IAH|
|   ABQ| LAS|
|   ABQ| LAX|
|   ABQ| LBB|
|   ABQ| MAF|
|   ABQ| MCI|
|   ABQ| ORD|
|   ABQ| PHX|
+------+----+
only showing top 20 rows

+------+----+
|Origin|Dest|
+------+----+
|   ABE| ATL|
|   ABE| BWI|
|   ABE| CLT|
|   ABE| DTW|
|   ABE| LGA|
|   ABE| MDT|
|   ABE| ORD|
|   ABE| PIT|
|   ABE| RDU|
|   ABQ| AMA|
|   ABQ| ATL|
|   ABQ| CVG|
|   ABQ| DAL|
|   ABQ| DEN|
|   ABQ| DFW|
|   ABQ| ELP|
|   ABQ| IAH|
|   ABQ| LAS|
|   ABQ| LAX|
|   ABQ| LBB|
+------+----+
only showing top 20 rows
```

### 3. 2의 결과물의 row 수 추출 (count)
-- 뽑아낸 결과값의 전체 row수가 전체 운항 노선 수 이다.
```scala
#!/usr/bin/env scala


us_carrier_1987_distinct_ds.count()
us_carrier_1993_distinct_ds.count()

----------------------------------------
Output:

resN: Long = 3464

resN+1: Long = 3425
```

### SQL문으로 표현
```scala
#!/usr/bin/env scala


// 1987년도
spark.sql("SELECT COUNT(DISTINCT(Origin, Dest)) AS Count 
	     FROM global_temp.us_carrier 
	    WHERE Year = 1987").show()

// 1993년도
spark.sql("SELECT COUNT(DISTINCT(Origin, Dest)) AS COUNT 
	     FROM global_temp.us_carrier 
	    WHERE Year = 1993").show()

---------------------------------------------------------------------
Output:

+-----+
|Count|
+-----+
| 3464|
+-----+

+-----+
|Count|
+-----+
| 3425|
+-----+
```

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


// 'UniqueCarrier' column만을 가지는 DataFrame
val carriers_only_df = us_carrier_df.select("UniqueCarrier")

// distinct() 메서드를 이용한 유니크 값 추출 (시간 소요가 조금 있다.)
val carriers_only_distinct_df = carriers_only_df.distinct()
// same as
// spark.sql("SELECT DISTINCT UniqueCarrier FROM global_temp.us_carrier").show()

carriers_only_distinct_df.show()


import org.apache.spark.sql.functions.col

// DL 항공만 추출
us_carrier_df.filter(col("UniqueCarrier") === "DL").show()
// same As
// us_carrier_df.filter($"UniqueCarrier" === "DL").show()

// 1990년도 데이터만 추출
us_carrier_df.filter(col("Year") === 1990).show()
// same As
// us_carrier_df.filter($"UniqueCarrier" === "DL").show()

// 위 두 명령을 합쳐보자.
// us_carrier_df.filter(col("UniqueCarrier") === "DL").filter(col("Year") === 1990).show()
us_carrier_df.filter((col("UniqueCarrier") === "DL") && (col("Year") === 1990).show()
// same As
// us_carrier_df.filter(($"UniqueCarrier" === "DL") && ($"Year" === 1990)).show()

// SQL문으로 작성시
spark.sql("SELECT * 
	     FROM global_temp.us_carrier 
	    WHERE UniqueCarrier == 'DL' AND Year == 1990").show()

// DL항공의 1990년도 운항 횟수
us_carrier_df.filter(($"UniqueCarrier" === "DL") && ($"Year") === 1990).count()

// 운항거리 최소, 최대, 평균
us_carrier_df.select(min($"distance"), max($"distance"), mean($"distance")).show()

// 운항거리가 0(최소)인 데이터 조회
us_carrier_df.filter($"distance" === 0).show()

// 운항거리가 4983(최대)인 데이터 조회
us_carrier_df.filter($"distance" === 4983).show()

// DataFrame.describe() 메서드로 한번에 알아보자.
us_carrier_df.describe("distance").show()


val us_carrier_1987_df = us_carrier_df.filter($"Year" === 1987).select($"Origin", $"Dest")
val us_carrier_1993_df = us_carrier_df.filter($"Year" === 1993).select($"Origin", $"Dest")

// 중복값 제거
val us_carrier_1987_distinct_ds = us_carrier_1987_df.distinct()
val us_carrier_1993_distinct_ds = us_carrier_1993_df.distinct()

// 정렬 후 출력
us_carrier_1987_distinct_ds.orderBy($"Origin", $"Dest").show()
us_carrier_1993_distinct_ds.orderBy($"Origin", $"Dest").show()

us_carrier_1987_distinct_ds.count()
us_carrier_1993_distinct_ds.count()

// SQL 문으로 표현
// 1987년도
spark.sql("SELECT COUNT(DISTINCT(Origin, Dest)) AS Count 
	     FROM global_temp.us_carrier 
	    WHERE Year = 1987").show()

// 1993년도
spark.sql("SELECT COUNT(DISTINCT(Origin, Dest)) AS COUNT 
	     FROM global_temp.us_carrier 
	    WHERE Year = 1993").show()
```                                                                   

---
                                                                      
## Spark Doc 한글문서                                                 
[https://spark-korea.github.io/docs/](https://spark-korea.github.io/docs/)
