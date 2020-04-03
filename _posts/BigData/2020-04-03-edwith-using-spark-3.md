---
date: 2020-04-03 10:38:51
layout: post
title: "[Spark를 활용한 데이터분석] 3. 얼마나 많은 항공사가 있을까?"
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
                                                                      
### 이전 강의: [2. 데이터 전처리](https://jhleeeme.github.io/edwith-using-spark-2/)
이전 강의까지 해서 데이터 전처리를 끝냈다.  
이제 전처리한 데이터를 가지고 분석해보자.  
이번 강의에선 ```DataFrame API```, ```SQL```을 활용해 항공사를 살펴보겠다.

---
## 항공사 살펴보기
1. ```DataFrame API```를 활용한 조회  
-- DataFrame#```select()```  
-- DataFrame#```distinct()```
2. ```SQL```을 활용한 조회  

### 1. DataFrame API
-- ```select()``` 메서드를 이용한 ```'UniqueCarrier'``` column 추출
```scala
#!/usr/bin/env scala


val carriers_only_df = us_carrier_df.select("UniqueCarrier")

carriers_only_df.show(5)

--------------------------------------
Output:

+-------------+
|UniqueCarrier|
+-------------+
|           PI|
|           PI|
|           PI|
|           PI|
|           PI|
+-------------+
only showing top 5 rows
```

-- ```distinct()``` 메서드를 이용한 유니크 값 추출 (시간 소요가 조금 있다.)
```scala
#!/usr/bin/env scala


val carriers_only_distinct_df = carriers_only_df.distinct()

carriers_only_distinct_df.show()

----------------------------------------
Output:

+-------------+
|UniqueCarrier|
+-------------+
|           UA|
|           EA|
|           PI|
|           PS|
|           AA|
|           NW|
|           EV|
|           B6|
|           HP|
|           TW|
|           DL|
|           OO|
|           F9|
|           YV|
|           TZ|
|           US|
|           AQ|
|           MQ|
|           OH|
|           HA|
+-------------+
only showing top 20 rows
```

### 1. SQL을 활용한 조회
-- 마찬가지로 ```DataFrame API``` 호출 형태로 변환되어 처리되므로 결과는 같다.
```scala
#!/usr/bin/env scala


spark.sql("SELECT DISTINCT UniqueCarrier FROM global_temp.us_carrier").show()

----------------------------------------------------------------
Output:

+-------------+
|UniqueCarrier|
+-------------+
|           UA|
|           EA|
|           PI|
|           PS|
|           AA|
|           NW|
|           EV|
|           B6|
|           HP|
|           TW|
|           DL|
|           OO|
|           F9|
|           YV|
|           TZ|
|           US|
|           AQ|
|           MQ|
|           OH|
|           HA|
+-------------+
only showing top 20 rows
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
```                                                                   
                                                                      
---                                                                   
                                                                      
## Spark Doc 한글문서                                                 
[https://spark-korea.github.io/docs/](https://spark-korea.github.io/docs/)
