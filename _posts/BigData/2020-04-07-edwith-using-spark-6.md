---
date: 2020-04-07 07:21:26
layout: post
title: "[Spark를 활용한 데이터분석] 6. 가장 바쁜 공항 TOP10"
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
-- DataFrame.```groupBy()```  
-- DataFrame.```count()```  
-- DataFrame.```orderBy()```  
-- ```spark.sql.functions.desc``` (내림차순 메서드)

이전 강의에선 운항 노선에 대해 알아보았다.  
이번엔 전 세계에서 가장 바쁜 공항을 알아보자.
### 이전 강의: [5. 운항 노선 알아보기](https://jhleeeme.github.io/edwith-using-spark-5/)

---

# 전 세계에서 가장 바쁜 공항은?

-- record를 일정한 기준으로 한 번 묶은 뒤 그 안에서 집계 연산을 수행

-- 접근 절차  
ㄴ> 1. 각 record를 도착 공항 ```'Dest'``` 기준으로 묶고 ```(groupBy)```  
ㄴ> 2. 1에서 묶은 데이터의 row 수 카운팅 (=도착 수) ```(count)```  
ㄴ> 3. 2에서 추출한 DataFrame을 ```'도착 수'``` 기준 내림차순 정렬 ```(orderBy)```

### 1. 각 record를 도착 공항 ```Dest``` 기준으로 묶어보자. (groupBy)
-- DataFrame.```groupBy()``` 메서드를 사용  
ㄴ> return ```org.apache.spark.sql.RelationalGroupedDataset```
```scala
#!/usr/bin/env scala


val dest_by_arrival_groupedDS = us_carrier_df.groupBy($"Dest")

-----------------------------------------------------
Output:


dest_by_arrival_groupedDS: org.apache.spark.sql.RelationalGroupedDataset = org.apache.spark.sql.RelationalGroupedDataset@28eb6ebd
```

### 2. 묶은 데이터의 row 수 카운팅 (=도착 수) (count)
-- 물론 RelationalGroupedDataset 클래스에도 ```count()``` 메서드가 있음.  
ㄴ> return ```org.apache.spark.sql.DataFrame```

```scala
#!/usr/bin/env scala


val dest_by_arrival_count_df = dest_by_arrival_groupedDS.count()

dest_by_arrival_count_df.show()

----------------------------------
Output:

dest_by_arrival_count_df: org.apache.spark.sql.DataFrame = [Dest: string, count: bigint]

+----+------+
|Dest| count|
+----+------+
| BGM| 26330|
| DLG|  4940|
| PSE|  2929|
| INL|   290|
| MSY|951585|
| GEG|262264|
| BUR|579731|
| SNA|820658|
| GTF| 60822|
| GRB| 86390|
| IDA| 35521|
| GRR|240478|
| LWB|  1697|
| PVU|    11|
| EUG| 72786|
| PSG| 14992|
| GSO|386589|
| PVD|440405|
| ISO|  5906|
| MYR| 82534|
+----+------+
only showing top 20 rows
```
-- Zeppelin으로 작업할 시 ```z.show()``` 메서드를 써보자. 간단하게 시각화 가능
<a href="https://user-images.githubusercontent.com/31606119/78661319-88805600-7909-11ea-8f25-a66c67566013.png">
![0](https://user-images.githubusercontent.com/31606119/78661319-88805600-7909-11ea-8f25-a66c67566013.png)
</a>

### 3. 도착 수 기준 내림차순 정렬 (orderBy)
-- ```org.apache.spark.sql.functions.desc()``` 활용  
ㄴ> 인자 값으로 Column의 스트링값을 받는다. ```[error: (desc($"count"))]```
```scala
#!/usr/bin/env scala


import org.apache.spark.sql.functions.desc

dest_by_arrival_count_df.orderBy(desc("count")).show(10)
// same As
//dest_by_arrival_count_df.orderBy(-$"count").show(10)

------------------------------------------------------
Output:

+----+-------+
|Dest|  count|
+----+-------+
| ORD|6638035|
| ATL|6094186|
| DFW|5745593|
| LAX|4086930|
| PHX|3497764|
| DEN|3335222|
| DTW|2997138|
| IAH|2889971|
| MSP|2765191|
| SFO|2725676|
+----+-------+
only showing top 10 rows
```
가장 바쁜 공항 top10을 뽑아냈다.  
전 세계에서 가장 복잡한 공항 중 하나인 시카고의 오헤어 국제공항(ORD)이 1위 이다.  
아래는 SQL문으로 작성해 보았다.

### SQL문으로 표현
```scala
#!/usr/bin/env scala


spark.sql("SELECT Dest, COUNT(*) AS Count 
	     FROM global_temp.us_carrier 
	    GROUP BY Dest 
	    ORDER BY Count DESC LIMIT 10").show()

--------------------------------------------
Output:

+----+-------+
|Dest|  Count|
+----+-------+
| ORD|6638035|
| ATL|6094186|
| DFW|5745593|
| LAX|4086930|
| PHX|3497764|
| DEN|3335222|
| DTW|2997138|
| IAH|2889971|
| MSP|2765191|
| SFO|2725676|
+----+-------+
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
import org.apache.spark.sql.functions.{min, max, mean}

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

// 6. 가장 바쁜 공항 TOP10
// 목적지 기준으로 그룹핑
val dest_by_arrival_groupedDS = us_carrier_df.groupBy($"Dest")

// row 카운팅 (=도착 수)
val dest_by_arrival_count_df = dest_by_arrival_groupedDS.count()

dest_by_arrival_count_df.show()

// 내림차순 정렬 메서드
import org.apache.spark.sql.functions.desc

// 도착 수 기준으로 내림차순 정렬
dest_by_arrival_count_df.orderBy(desc("count")).show(10)
// same As
//dest_by_arrival_count_df.orderBy(-$"count").show(10)

// SQL 문으로 표현
spark.sql("SELECT Dest, COUNT(*) AS Count 
	     FROM global_temp.us_carrier 
	    GROUP BY Dest 
	    ORDER BY Count DESC LIMIT 10").show()
```                                                                   

---
                                                                      
## Spark Doc 한글문서                                                 
[https://spark-korea.github.io/docs/](https://spark-korea.github.io/docs/)
