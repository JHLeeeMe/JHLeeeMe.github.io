---
date: 2020-04-09 07:20:26
layout: post
title: "[Spark를 활용한 데이터분석] 7. 실제와 예상 비행시간의 차이가 가장 큰 노선은?"
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
-- DataFrame.```join()```  
-- ```spark.sql.functions.abs()```  
-- Column.```alias()```

이전 강의에선 세계에서 가장 바쁜 공항을 알아보았다.  
이번엔 실제 비행시간과 예상 비행시간의 차이가 가장 큰 노선을 알아보자.
### 이전 강의: [6. 가장 바쁜 공항 TOP10](https://jhleeeme.github.io/edwith-using-spark-6/)

---

## 실제와 예상 비행시간의 차이가 가장 큰 노선
-- 노선별로 그룹핑된 데이터를 가지고 작업을 하겠다.  

-- 작업 절차  
ㄴ> 1. record를 노선별로 추출  
ㄴ> 2. ```1```에서 추출한 데이터의 실제 비행시간 평균을 계산  
ㄴ> 3. ```1```에서 추출한 데이터의 예상 비행시간을 구한다.  
ㄴ> 4. ```2, 3```을 하나로 모은다.    
ㄴ> 5. ```4```에서 구한 데이터의 실제와 예상 비행시간의 차이를 구한다.  
ㄴ> 6. 결과값을 내림차순 정렬한다.

### 1. record를 노션별로 추출
```scala
#!/usr/bin/env scala


val origin_dest_groupedDS = us_carrier_df.groupBy($"Origin", $"Dest")

----------------------------------------------------------------------
Output:

origin_dest_groupedDS: org.apache.spark.sql.RelationalGroupedDataset = org.apache.spark.sql.RelationalGroupedDataset@73df0abd
```

### 2. 실제 비행시간 평균 계산
-- RelationalGroupedDataset.```mean()``` 메서드 활용 (인자로 String값만 받는다.)  
ㄴ> return ```org.apache.spark.sql.DataFrame```
```scala
#!/usr/bin/env scala


val actual_elapsed_df = origin_dest_groupedDS.mean("ActualElapsedTime")

actual_elapsed_df.show()

--------------------------------------------------
Output:

actual_elapsed_df: org.apache.spark.sql.DataFrame = [Origin: string, Dest: string ... 1 more field]

+------+----+----------------------+
|Origin|Dest|avg(ActualElapsedTime)|
+------+----+----------------------+
|   MCI| MKE|     81.12094395280236|
|   ATL| GSP|     51.52835697846175|
|   PBI| DCA|    135.44469182095148|
|   PHL| MCO|    145.21072827655723|
|   EWR| STT|    242.35271128480704|
|   SNA| PHX|      71.9300320105966|
|   STX| JFK|     238.1065595022038|
|   DAY| JFK|    105.62045760430686|
|   MCI| IAH|    119.87866268166901|
|   ROR| YAP|    53.132132132132135|
|   ORD| PDX|    257.65082073928113|
|   ROC| CLE|     69.20860685630926|
|   GSP| PIT|     76.87394957983193|
|   GRR| PIT|      65.0678077682686|
|   SMF| BUR|     64.23738942963128|
|   TOL| STL|     86.31593406593407|
|   ICT| LIT|     60.18518518518518|
|   LNK| OMA|    25.044642857142858|
|   SPI| ORD|     59.05182045848584|
|   CLE| SJU|     256.5263653483992|
+------+----+----------------------+
only showing top 20 rows
```

### 3. 예상 비행시간 구하기
-- 예상 비행시간은 항상 동일하므로 편의상 최솟값을 추출
```scala
#!/usr/bin/env scala


val expected_elapsed_df = origin_dest_groupedDS.min("CRSElapsedTime")

expected_elapsed_df.show()

---------------------------------------------------------------------
Output:

expected_elapsed_df: org.apache.spark.sql.DataFrame = [Origin: string, Dest: string ... 1 more field]

+------+----+-------------------+
|Origin|Dest|min(CRSElapsedTime)|
+------+----+-------------------+
|   MCI| MKE|                 75|
|   ATL| GSP|                 37|
|   PBI| DCA|                 60|
|   PHL| MCO|                 42|
|   EWR| STT|                219|
|   SNA| PHX|                 55|
|   STX| JFK|                190|
|   DAY| JFK|                 90|
|   MCI| IAH|                104|
|   ROR| YAP|                 50|
|   ORD| PDX|                237|
|   ROC| CLE|                 44|
|   GSP| PIT|                 55|
|   GRR| PIT|                 50|
|   SMF| BUR|                 60|
|   TOL| STL|                 74|
|   ICT| LIT|                 61|
|   LNK| OMA|                 24|
|   SPI| ORD|                 52|
|   CLE| SJU|                249|
+------+----+-------------------+
only showing top 20 rows
```

### 4. 2, 3에서 추출한 데이터를 하나로 모으기
-- DataFrame.```join()``` 메서드 활용  
```scala
// join() 메서드 e.g.
 
df1.join(df2, Seq("column_name1", ["column_name2", ...]), ["join_type"])
```

-- 같은 그루핑 데이터셋에서 뽑아낸 것이므로 ```row수, Origin, Dest```가 같을 것이다. LEFT OUTER JOIN을 쓰자.
```scala
#!/usr/bin/env scala


val left_outer_joined_df = actual_elapsed_df
  .join(expected_elapsed_df, Seq("Origin", "Dest"), "left_outer")
    
left_outer_joined_df.show()

---------------------------------------------------
Output:

left_outer_joined_df: org.apache.spark.sql.DataFrame = [Origin: string, Dest: string ... 2 more fields]

+------+----+----------------------+-------------------+
|Origin|Dest|avg(ActualElapsedTime)|min(CRSElapsedTime)|
+------+----+----------------------+-------------------+
|   ADK| AKN|    128.50135501355012|                117|
|   ATL| GSP|     51.52835697846175|                 37|
|   AVP| JFK|     71.47058823529412|                 60|
|   BDL| SLC|     310.3319502074689|                305|
|   BFL| SAN|    54.772887323943664|                 55|
|   BOI| SBA|    220.92424242424244|                191|
|   BQN| MCO|    162.22651933701658|                125|
|   BUR| MRY|                  null|                 70|
|   CLE| SJU|     256.5263653483992|                249|
|   DAY| JFK|    105.62045760430686|                 90|
|   DSM| EWR|     153.5728155339806|                135|
|   EWR| STT|    242.35271128480704|                219|
|   FSD| ATL|     148.8791390728477|                145|
|   GRR| PIT|      65.0678077682686|                 50|
|   GSP| PIT|     76.87394957983193|                 55|
|   HTS| MCN|                  95.0|                 75|
|   ICT| LIT|     60.18518518518518|                 61|
|   LAS| LIT|    173.08053923263049|                170|
|   LAX| OXR|    30.822375511963784|                 26|
|   LNK| OMA|    25.044642857142858|                 24|
+------+----+----------------------+-------------------+
only showing top 20 rows
```

### 참고) JoinType
```scala
object JoinType {
  def apply(typ: String): JoinType = typ.toLowerCase(Locale.ROOT).replace("_", "") match {
    case "inner" => Inner
    case "outer" | "full" | "fullouter" => FullOuter
    case "leftouter" | "left" => LeftOuter
    case "rightouter" | "right" => RightOuter
    case "leftsemi" => LeftSemi
    case "leftanti" => LeftAnti
    case "cross" => Cross
```

### 5. 4에서 구한 데이터의 실제와 예상 비행시간의 차이
-- ```spark.sql.functions.abs()``` 메서드 활용
```scala
#!/usr/bin/env scala


import org.apache.spark.sql.functions.abs

val difference_df = left_outer_joined_df.select($"Origin", $"Dest", abs($"avg(ActualElapsedTime)" - $"min(CRSElapsedTime)")

difference_df.show()

----------------------------------------------
Output:

import org.apache.spark.sql.functions.abs
difference_df: org.apache.spark.sql.DataFrame = [Origin: string, Dest: string ... 1 more field]

+------+----+---------------------------------------------------+
|Origin|Dest|abs((avg(ActualElapsedTime) - min(CRSElapsedTime)))|
+------+----+---------------------------------------------------+
|   ADK| AKN|                                 11.501355013550125|
|   ATL| GSP|                                 14.528356978461751|
|   AVP| JFK|                                 11.470588235294116|
|   BDL| SLC|                                 5.3319502074688785|
|   BFL| SAN|                                0.22711267605633623|
|   BOI| SBA|                                 29.924242424242436|
|   BQN| MCO|                                  37.22651933701658|
|   BUR| MRY|                                               null|
|   CLE| SJU|                                  7.526365348399224|
|   DAY| JFK|                                 15.620457604306864|
|   DSM| EWR|                                 18.572815533980588|
|   EWR| STT|                                 23.352711284807043|
|   FSD| ATL|                                 3.8791390728476927|
|   GRR| PIT|                                 15.067807768268594|
|   GSP| PIT|                                  21.87394957983193|
|   HTS| MCN|                                               20.0|
|   ICT| LIT|                                 0.8148148148148167|
|   LAS| LIT|                                 3.0805392326304855|
|   LAX| OXR|                                  4.822375511963784|
|   LNK| OMA|                                 1.0446428571428577|
+------+----+---------------------------------------------------+
only showing top 20 rows
```

### 6. 결과값 내림차순 정렬
-- DataFrame.```orderBy()``` 메서드 활용  
-- Column 값이 매우 지저분하므로 Column.```alias()``` 메서드로 정리
```scala
#!/usr/bin/env scala


difference_df
  .select($"Origin", $"Dest", $"abs((avg(ActualElapsedTime) - min(CRSElapsedTime)))".alias("difference"))
  .orderBy(desc("difference"))
  .show()

--------------------------------------------------------------------
Output:

+------+----+------------------+
|Origin|Dest|        difference|
+------+----+------------------+
|   JFK| AUS|1476.2607558139534|
|   VPS| DHN|             512.0|
|   PHX| JFK| 444.1817228226319|
|   LIH| LAX|396.62511291779583|
|   BOS| LAX| 319.5572305229456|
|   ORD| HNL|318.56579033632727|
|   PHX| DCA|315.28186109238027|
|   SEA| BOS| 314.1135195080149|
|   DFW| OGG|308.03667563930014|
|   SNA| ORD|307.26533535848665|
|   HNL| ORD|305.55223880597015|
|   DFW| HNL| 299.7765151515151|
|   DEN| HNL|299.72057646116895|
|   JFK| SAN|292.30877054717405|
|   OAK| IAD| 291.5955174841586|
|   LGB| FLL|289.18823529411765|
|   LAX| BOS|283.17967048310527|
|   BOS| LAS| 278.0052805280528|
|   SBA| DFW| 277.7184615384615|
|   BUR| DFW|276.51984257133483|
+------+----+------------------+
only showing top 20 rows
```

### SQL문으로 표현
```scala
#!/usr/bin/env scala


// Global Temp View 생성
actual_elapsed_df.createOrReplaceGlobalTempView("actual_elapsed")
expected_elapsed_df.createOrReplaceGlobalTempView("expected_elapsed")

spark.sql("""
  SELECT a.Origin, a.Dest, abs(a.`avg(ActualElapsedTime)` - e.`min(CRSElapsedTime)`) AS difference 
    FROM global_temp.actual_elapsed AS a LEFT OUTER JOIN global_temp.expected_elapsed AS e 
      ON a.Origin = e.Origin AND a.Dest = e.Dest 
   ORDER BY difference DESC
""").show()
---------------------------------
Output:

+------+----+------------------+
|Origin|Dest|        difference|
+------+----+------------------+
|   JFK| AUS|1476.2607558139534|
|   VPS| DHN|             512.0|
|   PHX| JFK| 444.1817228226319|
|   LIH| LAX|396.62511291779583|
|   BOS| LAX| 319.5572305229456|
|   ORD| HNL|318.56579033632727|
|   PHX| DCA|315.28186109238027|
|   SEA| BOS| 314.1135195080149|
|   DFW| OGG|308.03667563930014|
|   SNA| ORD|307.26533535848665|
|   HNL| ORD|305.55223880597015|
|   DFW| HNL| 299.7765151515151|
|   DEN| HNL|299.72057646116895|
|   JFK| SAN|292.30877054717405|
|   OAK| IAD| 291.5955174841586|
|   LGB| FLL|289.18823529411765|
|   LAX| BOS|283.17967048310527|
|   BOS| LAS| 278.0052805280528|
|   SBA| DFW| 277.7184615384615|
|   BUR| DFW|276.51984257133483|
+------+----+------------------+
only showing top 20 rows
```
예상 시간과 가장 큰 차이가 나는 노선은  
```존.F.케네디(JFK) 공항```에서 ```오스틴 버그스트롬(AUS) 공항```으로 가는 노선으로 나온다.  
대체로 공항 간의 거리가 멀수록, 공항이 붐빌수록 차이가 심하게 나는 것 같다.  
이 부분에 대한 검증은 생략

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
spark.sql("SELECT * FROM global_temp.us_carrier LIMIT 10")


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
spark.sql("""
  SELECT * 
    FROM global_temp.us_carrier 
   WHERE UniqueCarrier == 'DL' AND Year == 1990
""").show()

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
spark.sql("""
  SELECT COUNT(DISTINCT(Origin, Dest)) AS Count 
    FROM global_temp.us_carrier 
   WHERE Year = 1987
""").show()

// 1993년도
spark.sql("""
  SELECT COUNT(DISTINCT(Origin, Dest)) AS COUNT 
    FROM global_temp.us_carrier 
   WHERE Year = 1993
""").show()


// 6. 가장 바쁜 공항 TOP10
// 6-1. 목적지 기준으로 그룹핑
val dest_by_arrival_groupedDS = us_carrier_df.groupBy($"Dest")

// 6-2. row 카운팅 (=도착 수)
val dest_by_arrival_count_df = dest_by_arrival_groupedDS.count()

dest_by_arrival_count_df.show()

// 6-3. 내림차순 정렬 메서드
import org.apache.spark.sql.functions.desc

// 6-4. 도착 수 기준으로 내림차순 정렬
dest_by_arrival_count_df.orderBy(desc("count")).show(10)
// same As
//dest_by_arrival_count_df.orderBy(-$"count").show(10)

// 6-5. SQL 문으로 표현
spark.sql("""
  SELECT Dest, COUNT(*) AS Count 
    FROM global_temp.us_carrier 
   GROUP BY Dest 
   ORDER BY Count DESC LIMIT 10
""").show()


// 7. 실제와 예상 비행시간의 차이가 가장 큰 노선은?
// 7-1. 그룹핑으로 노선 데이터셋 추출
val origin_dest_groupedDS = us_carrier_df.groupBy($"Origin", $"Dest")

// 7-2. 실제 비행시간 평균 계산
val actual_elapsed_df = origin_dest_groupedDS.mean("ActualElapsedTime")

actual_elapsed_df.show()

// 7-3. 예상 비행시간 구하기
val expected_elapsed_df = origin_dest_groupedDS.min("CRSElapsedTime")

expected_elapsed_df.show()

// 7-4. 두 데이터 합치기 (Join)
val left_outer_joined_df = actual_elapsed_df
  .join(expected_elapsed_df, Seq("Origin", "Dest"), "left_outer")
    
left_outer_joined_df.show()

// 7-5. 실제와 예상 비행시간의 차이를 구하기
import org.apache.spark.sql.functions.abs

val difference_df = left_outer_joined_df.select($"Origin", $"Dest", abs($"avg(ActualElapsedTime)" - $"min(CRSElapsedTime)")

difference_df.show()

// 7-6. 결과값 정렬
difference_df
  .select($"Origin", $"Dest", $"abs((avg(ActualElapsedTime) - min(CRSElapsedTime)))".alias("difference"))
  .orderBy(desc("difference"))
  .show()

// 7-7. SQL문으로 표현
// Global Temp View 생성
actual_elapsed_df.createOrReplaceGlobalTempView("actual_elapsed")
expected_elapsed_df.createOrReplaceGlobalTempView("expected_elapsed")

spark.sql("""
  SELECT a.Origin, a.Dest, abs(a.`avg(ActualElapsedTime)` - e.`min(CRSElapsedTime)`) AS difference 
    FROM global_temp.actual_elapsed AS a LEFT OUTER JOIN global_temp.expected_elapsed AS e 
      ON a.Origin = e.Origin AND a.Dest = e.Dest 
   ORDER BY difference DESC
""").show()
```

---
                                                                      
## Spark Doc 한글문서                                                 
[https://spark-korea.github.io/docs/](https://spark-korea.github.io/docs/)
