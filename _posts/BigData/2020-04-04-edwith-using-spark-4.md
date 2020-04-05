---
date: 2020-04-04 09:15:40
layout: post
title: "[Spark를 활용한 데이터분석] 4. 항공사들의 운항거리, 횟수 톺아보기"
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
-- DataFrame.```describe()```  
-- ```spark.sql.functions.col()``` (Column 지정 메서드)  
-- ```spark.sql.functions.{min, max, mean}```

이전 강의에선 어떤 항공사들이 있는지 살펴보았다.   
이번엔 항공사들의 운항거리와 횟수를 살펴보자.
### 이전 강의: [3. 얼마나 많은 항공사가 있을까?](https://jhleeeme.github.io/edwith-using-spark-3/)

---

# 운항거리 & 횟수 톺아보기
-- 1. DL 항공사의 1990년도 운항 횟수 알아보기  
-- 2. 운항 거리의 최소, 최대, 평균 알아보기

## 1. DL 항공사의 1990년도 운항 횟수는?
-- ```filter()``` 메서드를 이용해 조건 설정  
-- ```col()``` 메서드로 column 지정
```scala
#!/usr/bin/env scala


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

---------------------------------------------------------------
Output:

+----+-----+----------+---------+-------------+---------+-------+-----------------+--------------+------+----+--------+---------+--------+
|Year|Month|DayofMonth|DayOfWeek|UniqueCarrier|FlightNum|TailNum|ActualElapsedTime|CRSElapsedTime|Origin|Dest|Distance|Cancelled|Diverted|
+----+-----+----------+---------+-------------+---------+-------+-----------------+--------------+------+----+--------+---------+--------+
|1990|    1|        20|        6|           DL|      229|   null|              184|           181|   ATL| DEN|    1199|    false|   false|
|1990|    1|        21|        7|           DL|      229|   null|              182|           181|   ATL| DEN|    1199|    false|   false|
|1990|    1|        22|        1|           DL|      229|   null|              192|           181|   ATL| DEN|    1199|    false|   false|
|1990|    1|        23|        2|           DL|      229|   null|              193|           181|   ATL| DEN|    1199|    false|   false|
|1990|    1|        24|        3|           DL|      229|   null|              182|           181|   ATL| DEN|    1199|    false|   false|
|1990|    1|        25|        4|           DL|      229|   null|              181|           181|   ATL| DEN|    1199|    false|   false|
|1990|    1|        26|        5|           DL|      229|   null|              184|           181|   ATL| DEN|    1199|    false|   false|
|1990|    1|        27|        6|           DL|      229|   null|              191|           181|   ATL| DEN|    1199|    false|   false|
|1990|    1|        28|        7|           DL|      229|   null|              186|           181|   ATL| DEN|    1199|    false|   false|
|1990|    1|        29|        1|           DL|      229|   null|              187|           181|   ATL| DEN|    1199|    false|   false|
|1990|    1|        30|        2|           DL|      229|   null|              176|           181|   ATL| DEN|    1199|    false|   false|
|1990|    1|        31|        3|           DL|      229|   null|              184|           181|   ATL| DEN|    1199|    false|   false|
|1990|    1|         1|        1|           DL|      229|   null|               81|            91|   RIC| ATL|     481|    false|   false|
|1990|    1|         2|        2|           DL|      229|   null|               88|            91|   RIC| ATL|     481|    false|   false|
|1990|    1|         3|        3|           DL|      229|   null|               84|            91|   RIC| ATL|     481|    false|   false|
|1990|    1|         4|        4|           DL|      229|   null|               96|            91|   RIC| ATL|     481|    false|   false|
|1990|    1|         5|        5|           DL|      229|   null|              105|            91|   RIC| ATL|     481|    false|   false|
|1990|    1|         6|        6|           DL|      229|   null|               91|            91|   RIC| ATL|     481|    false|   false|
|1990|    1|         7|        7|           DL|      229|   null|              123|            91|   RIC| ATL|     481|    false|   false|
|1990|    1|         8|        1|           DL|      229|   null|               88|            91|   RIC| ATL|     481|    false|   false|
+----+-----+----------+---------+-------------+---------+-------+-----------------+--------------+------+----+--------+---------+--------+
only showing top 20 rows 
```

-- 이제 운항 횟수를 알아보자.  
-- 위에서 추출한 데이터 프레임의 row수가 우리가 원하는 그것이다.
```scala
#!/usr/bin/env scala


us_carrier_df.filter(($"UniqueCarrier" === "DL") && ($"Year") === 1990).count()

-------------------------------------
Output:

resN: Long = 824062
```

## 2. 운항 거리의 최소, 최대, 평균 알아보기
-- 특정한 column값 전체에 수행되는 연산을 ```집계 연산(aggregation operation)``` 이라고 한다.  
-- 관련 기능들은 ```org.apache.spark.sql.functions``` 안에 모여있다.  
-- ```min(), max(), mean()``` 메서드들을 사용해서 집계 연산을 해보자.
```scala
#!/usr/bin/env scala


us_carrier_df.select(min($"distance"), max($"distance"), mean($"distance")).show()

-----------------------------------------------
Output:

+-------------+-------------+-----------------+
|min(distance)|max(distance)|    avg(distance)|
+-------------+-------------+-----------------+
|            0|         4983|701.6985307716058|
+-------------+-------------+-----------------+

import org.apache.spark.sql.functions.{min, mean, max}
```
-- 최대거리는 4983, 평균 701이 나왔다.  
-- 그런데 이상하게 최소거리가 0인 비행 기록이 있다.

-- 어떤 비행인지 살펴보자.
```scala
#!/usr/bin/env scala


us_carrier_df.filter($"distance" === 0).show()

-----------------------------------------------
Output:

+----+-----+----------+---------+-------------+---------+-------+-----------------+--------------+------+----+--------+---------+--------+
|Year|Month|DayofMonth|DayOfWeek|UniqueCarrier|FlightNum|TailNum|ActualElapsedTime|CRSElapsedTime|Origin|Dest|Distance|Cancelled|Diverted|
+----+-----+----------+---------+-------------+---------+-------+-----------------+--------------+------+----+--------+---------+--------+
|1989|   10|        24|        2|           US|     2503|   null|               48|            61|   LAX| LAX|       0|    false|   false|
|1989|   10|         3|        2|           AA|       83|   null|             null|           245|   ORD| ORD|       0|    false|    true|
|1987|   10|        25|        7|           EA|      759|   null|             null|            20|   FLL| FLL|       0|    false|    true|
|1987|   10|        14|        3|           HP|      206|   null|             null|            50|   LAS| LAS|       0|    false|    true|
|1987|   10|         5|        1|           HP|      857|   null|             null|            60|   PHX| PHX|       0|     true|   false|
|1987|   10|         4|        7|           EA|        2|   null|             null|            20|   LGA| LGA|       0|    false|    true|
|1987|   10|        18|        7|           EA|      152|   null|             null|            25|   JFK| JFK|       0|    false|    true|
+----+-----+----------+---------+-------------+---------+-------+-----------------+--------------+------+----+--------+---------+--------+
```
-- 운항이 취소되거나(Cancelled), 이륙 후 회항(Diverted)한 항공편들이다.  
ㄴ> 이 경우 출발지(Origin)와 도착지(Dest)가 같은 것도 특징이다.

-- 그렇다면 가장 먼거리를 운항한 항공편들을 살펴보자.
```scala
#!/usr/bin/env scala


us_carrier_df.filter($"distance" === 4983).show()

-----------------------------------------------------
Output:

+----+-----+----------+---------+-------------+---------+-------+-----------------+--------------+------+----+--------+---------+--------+
|Year|Month|DayofMonth|DayOfWeek|UniqueCarrier|FlightNum|TailNum|ActualElapsedTime|CRSElapsedTime|Origin|Dest|Distance|Cancelled|Diverted|
+----+-----+----------+---------+-------------+---------+-------+-----------------+--------------+------+----+--------+---------+--------+
|1988|    1|         1|        5|       PA (1)|       17|   null|              689|           645|   JFK| HNL|    4983|    false|   false|
|1988|    1|         2|        6|       PA (1)|       17|   null|              674|           645|   JFK| HNL|    4983|    false|   false|
|1988|    1|         8|        5|       PA (1)|       17|   null|             null|           645|   JFK| HNL|    4983|     true|   false|
|1988|    1|         9|        6|       PA (1)|       17|   null|              692|           645|   JFK| HNL|    4983|    false|   false|
|1988|    1|        15|        5|       PA (1)|       17|   null|              660|           645|   JFK| HNL|    4983|    false|   false|
|1988|    1|        16|        6|       PA (1)|       17|   null|              690|           645|   JFK| HNL|    4983|    false|   false|
|1988|    1|        22|        5|       PA (1)|       17|   null|              590|           645|   JFK| HNL|    4983|    false|   false|
|1988|    1|        23|        6|       PA (1)|       17|   null|              643|           645|   JFK| HNL|    4983|    false|   false|
|1988|    1|        29|        5|       PA (1)|       17|   null|              672|           645|   JFK| HNL|    4983|    false|   false|
|1988|    1|         1|        5|       PA (1)|       18|   null|              501|           540|   HNL| JFK|    4983|    false|   false|
|1988|    1|         2|        6|       PA (1)|       18|   null|              504|           540|   HNL| JFK|    4983|    false|   false|
|1988|    1|         8|        5|       PA (1)|       18|   null|             null|           540|   HNL| JFK|    4983|     true|   false|
|1988|    1|         9|        6|       PA (1)|       18|   null|              517|           540|   HNL| JFK|    4983|    false|   false|
|1988|    1|        15|        5|       PA (1)|       18|   null|              536|           540|   HNL| JFK|    4983|    false|   false|
|1988|    1|        16|        6|       PA (1)|       18|   null|              520|           540|   HNL| JFK|    4983|    false|   false|
|1988|    1|        22|        5|       PA (1)|       18|   null|              538|           540|   HNL| JFK|    4983|    false|   false|
|1988|    1|        23|        6|       PA (1)|       18|   null|              548|           540|   HNL| JFK|    4983|    false|   false|
|1988|    1|        29|        5|       PA (1)|       18|   null|              523|           540|   HNL| JFK|    4983|    false|   false|
|1988|    2|         6|        6|       PA (1)|       17|   null|              643|           645|   JFK| HNL|    4983|    false|   false|
|1988|    2|        12|        5|       PA (1)|       17|   null|              597|           645|   JFK| HNL|    4983|    false|   false|
+----+-----+----------+---------+-------------+---------+-------+-----------------+--------------+------+----+--------+---------+--------+
only showing top 20 rows
```
-- 뉴욕(JFK)에서 하와이(HNL)까지 바로 가는 항공편이다.

-- 여태까지 살펴본 최소, 최댓값 등의 통계치는 연속형 데이터에서 자주 살펴보게 되는 값들이다.  
ㄴ> spark는 내장 메서드로 DataFrame.```describe()``` 메서드를 제공한다.

-- 평균, 최소, 최대, 개수, 표준편차를 담고있는 ```DataFrame```을 return해준다.
```scala
#!/usr/bin/env scala


us_carrier_df.describe("distance").show()

------------------------------------------
Output:

+-------+-----------------+
|summary|         distance|
+-------+-----------------+
|  count|        123332969|
|   mean|701.6985307716058|
| stddev|551.2531700322845|
|    min|                0|
|    max|             4983|
+-------+-----------------+
```

### 다음 강의: [5. 운항 노선 알아보기](https://jhleeeme.github.io/edwith-using-spark-5/)

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
```                                                                   
                                                                      
---                                                                   
                                                                      
## Spark Doc 한글문서                                                 
[https://spark-korea.github.io/docs/](https://spark-korea.github.io/docs/)
