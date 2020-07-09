---
date: 2020-04-22 08:37:15
layout: post
title: "[Spark를 활용한 데이터분석] 마지막. 메서드 총정리"
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

남아있는 나머지 강의 3개는 비슷해서 생략하고  
여태까지 썼던 메서드들을 정리하기로 함.
### 이전 강의: [7. 실제와 예상 비행시간의 차이가 가장 큰 노선은?](https://jhleeeme.github.io/edwith-using-spark-7/)

---

키워드 총집합  
-- ```SparkSession```  
-- SparkSession.```read()```  
-- DataFrame.```printSchema()```  
-- DataFrame.```show()```  
-- DataFrame.```count()```  
-- DataFrame.```drop()```  
-- DataFrame.```withColumn()```  
-- DataFrame.```createOfReplaceGlobalTempView()```  
-- DataFrame.```filter()```  
-- DataFrame.```distinct()```  
-- DataFrame.```orderBy()```  
-- DataFrame.```select()```  
-- DataFrame.```groupBy()```  
-- DataFrame.```join()```  
-- DataFrame.```cache()```  
-- UDF (User Define Function)  
-- SparkSession.```sql()```  
-- spark.sql.functions.```col()```  
-- spark.sql.functions.```{min, max, mean}```  
-- spark.sql.functions.```desc()```  
-- spark.sql.functions.```abs()```  
-- Column.```alias()```

---

# 1. SparkSession
-- 세션 초기화 (모든 Spark 작업의 시작점.)  
```scala
#!/usr/bin/env scala


import org.apache.spark.sql.SparkSession

val spark = SparkSession
  .builder()
  .appName("Spark SQL basic example")
  .config("spark.some.config.option", "some-value")
  .getOrCreate()
```

-- 아래는 1.x 버전과 2.x 버전 비교
```scala
// Spark 1.6
val sparkConf = new SparkConf()
val sc = new SparkContext(sparkConf)
val sqlContext = new SQLContext(sc)
val df = sqlContext.read.json("data.json")
val tables = sqlContext.tables()

// Spark 2.x
val spark = SparkSession.builder.getOrCreate()
val df = spark.read.json("data.json")
val tables = spark.catalog.listTables()
```

# 2. DataFrame
-- 데이터프레임 초기화  
-- SparkSession.```read()```
```scala
val df = spark.read.json("<path>")
//same As
//val df = spark.read.format("json").load("<path>")

val df2 = spark.read
  .option("header", "true")
  .option("inferSchema", "true")
  .csv("<path>")
//same As
//val df2 = spark.read
//  .options(Map("header" -> "true", "inferSchema" -> "true"))
//  .csv("<path>")
//
//val df2 = spark.read.format("csv")
//  .options(Map("header" -> "true", "inferSchema" -> "true"))
//  .load("<path>")
```

# 3. DataFrame 조회
-- DataFrame.```printSchema()```  
-- DataFrame.```show()```  
-- Zeppelin상에선 ```z.show(DataFrame)```
```scala
// DataFrame의 구조 출력
raw_df.printSchema()

// 내용물 출력
raw_df.show()

----------------------
Output:

root
 |-- Year: integer (nullable = true)
 |-- Month: integer (nullable = true)
 |-- DayofMonth: integer (nullable = true)
 |-- DayOfWeek: integer (nullable = true)
 |-- DepTime: string (nullable = true)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+----+-----+----------+---------+-------+----------+-------+----------+-------------+---------+-------+-----------------+--------------+-------+--------+--------+------+----+--------+------+-------+---------+----------------+--------+------------+------------+--------+-------------+-----------------+
|Year|Month|DayofMonth|DayOfWeek|DepTime|CRSDepTime|ArrTime|CRSArrTime|UniqueCarrier|FlightNum|TailNum|ActualElapsedTime|CRSElapsedTime|AirTime|ArrDelay|DepDelay|Origin|Dest|Distance|TaxiIn|TaxiOut|Cancelled|CancellationCode|Diverted|CarrierDelay|WeatherDelay|NASDelay|SecurityDelay|LateAircraftDelay|
+----+-----+----------+---------+-------+----------+-------+----------+-------------+---------+-------+-----------------+--------------+-------+--------+--------+------+----+--------+------+-------+---------+----------------+--------+------------+------------+--------+-------------+-----------------+
|1988|    1|         9|        6|   1348|      1331|   1458|      1435|           PI|      942|     NA|               70|            64|     NA|      23|      17|   SYR| BWI|     273|    NA|     NA|        0|              NA|       0|          NA|          NA|      NA|           NA|               NA|
|1988|    1|        10|        7|   1334|      1331|   1443|      1435|           PI|      942|     NA|               69|            64|     NA|       8|       3|   SYR| BWI|     273|    NA|     NA|        0|              NA|       0|          NA|          NA|      NA|           NA|               NA|
|1988|    1|        11|        1|   1446|      1331|   1553|      1435|           PI|      942|     NA|               67|            64|     NA|      78|      75|   SYR| BWI|     273|    NA|     NA|        0|              NA|       0|          NA|          NA|      NA|           NA|               NA|
|1988|    1|        12|        2|   1334|      1331|   1438|      1435|           PI|      942|     NA|               64|            64|     NA|       3|       3|   SYR| BWI|     273|    NA|     NA|        0|              NA|       0|          NA|          NA|      NA|           NA|               NA|
|1988|    1|        13|        3|   1341|      1331|   1503|      1435|           PI|      942|     NA|               82|            64|     NA|      28|      10|   SYR| BWI|     273|    NA|     NA|        0|              NA|       0|          NA|          NA|      NA|           NA|               NA|
```

# 4. UDF 
-- User Define Function  
-- 이를테면 데이터의 null 처리 같은 작업을 하는 함수 정의  
-- DataFrame.```withColumn()```과 찰떡  
-- 먼저 udf함수로 쓸 함수를 정의하자  
-- 아래는 ```UDFs```라는 오브젝트안에 함수들을 모아둔 형태  
-- 불러올 때는 ```UDFs.메서드```꼴이 될것이다.
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

-- 위에서 만든 함수를 spark에서 쓰기위해 udf함수로
```scala
import org.apache.spark.sql.functions.udf

val stringToIntegerFunction = udf(UDFs.stringToInteger _)
val integerToBooleanFunction = udf(UDFs.integerToBoolean _)
```

# 5. DataFrame 변환
-- DataFrame.```drop()```  
-- DataFrame.```withColumn()```  
```scala
val us_carrier_df = raw_df
  .drop(
    // 사용하지 않을 column
    "DepTime", "CRSDepTime", ...)
  .withColumn(
    // 위에서 정의한 udf 함수를 사용하여 데이터 변환
    "Distance", stringToIntegerFunction(raw_df("Distance")))
  .withColumn(...)
```

# 6. 캐싱
-- 캐싱은 따로 정리할 예정이다.
```scala
us_carrier_df.cache()
```

# 7. 임시 뷰 생성
-- SparkSession.```sql()``` 을 통해 sql문으로 작업하기 위해 sql의 ```VIEW```을 생성한다고 생각하면 된다.  
-- ```GlobalTempView```의  ```TempView```과의 차이는 다른 세션에서도 사용할 수 있다는 것.
```scala
us_carrier_df.createTempView("<VIEW_NAME>")
us_carrier_df.createGlobalTempView("<VIEW_NAME>")
us_carrier_df.createOrReplaceTempView("<VIEW_NAME>")
us_carrier_df.createOrReplaceGlobalTempView("<VIEW_NAME>")
```

# 8. DataFrame 여러 메서드
-- ```sql문```을 통한 조회작업을 ```DataFrame API```를 사용해 처리할 수 있다.  
-- 아래 목록들을 sql문과 비교해보자.  
-- SparkSession.```sql()```  
-- 1. DataFrame.```select()```  
-- 2. DataFrame.```filter()```  
-- 3. DataFrame.```join()```  
-- 4. DataFrame.```distinct()```  
-- 5. DataFrame.```orderBy()```  
-- 6. DataFrame.```groupBy()```  
-- spark.sql.functions.```col()```  
-- spark.sql.functions.```abs()```  
-- spark.sql.functions.```{min, max, mean}```  
-- spark.sql.functions.```desc()```  
-- Column.```alias()```

## 8-1. select
-- SparkSession.```sql()```  
-- DataFrame.```select()```
```scala
// Global Temp View 생성
us_carrier_df.createOfReplaceGlobalTempView("us_carrier")

val carriers_only_df = us_carrier_df.select("UniqueCarrier")
val carriers_only_distinct_df = carriers_only_df.distinct()

carriers_only_distinct_df.show(10)

// SQL문으로 작성시
spark.sql("""
  SELECT DISTINCT UniqueCarrier
    FROM global_temp.us_carrier LIMIT 10
""").show()
```

## 8-2. filter
-- DataFrame.```filter()```  
-- spark.sql.functions.```col()```  
ㄴ> 칼럼 지정 메서드(scala에선 ```$"<columnName>"```으로 표현 가능)  
-- sql문의 ```WHERE```절
```scala
import org.apache.spark.sql.functions.col

// DL 항공만 추출
us_carrier_df.filter((col("UniqueCarrier") === "DL") && (col("Year") === 1990).select("Year").show()
// same As
// us_carrier_df.filter(($"UniqueCarrier" === "DL") && ($"Year" === 1990)).select("Year").show()

// SQL문으로 작성시
spark.sql("""
  SELECT Year 
    FROM global_temp.us_carrier 
   WHERE UniqueCarrier == 'DL' AND Year == 1990
""").show()
```

## 8-3. join
-- sql문의 ```Join```과 대응  
-- DataFrame.```join()```  
```scala
// join() 메서드 e.g.
 
df1.join(df2, Seq("column_name1", ["column_name2", ...]), ["join_type"])
```
### 참고) Join Type
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
### 사용 예)
```scala

// 실제와 예상 비행시간의 차이가 가장 큰 노선은?
// 그룹핑으로 노선 데이터셋 추출
val origin_dest_groupedDS = us_carrier_df.groupBy($"Origin", $"Dest")

// 실제 비행시간 평균 계산
val actual_elapsed_df = origin_dest_groupedDS.mean("ActualElapsedTime")

// 예상 비행시간 구하기
val expected_elapsed_df = origin_dest_groupedDS.min("CRSElapsedTime")

// 두 데이터 합치기 (Join)
val left_outer_joined_df = actual_elapsed_df
  .join(expected_elapsed_df, Seq("Origin", "Dest"), "left_outer")
    
left_outer_joined_df.show()

// 실제와 예상 비행시간의 차이를 구하기
import org.apache.spark.sql.functions.abs

val difference_df = left_outer_joined_df.select($"Origin", $"Dest", abs($"avg(ActualElapsedTime)" - $"min(CRSElapsedTime)")

difference_df.show()

// 결과값 정렬
difference_df
  .select($"Origin", $"Dest", $"abs((avg(ActualElapsedTime) - min(CRSElapsedTime)))".alias("difference"))
  .orderBy(desc("difference"))
  .show()

// SQL문으로 표현
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


### 나머지는 코드로만
```scala
import org.apache.spark.sql.functions.{min, max, mean}

// 운항거리 최소, 최대, 평균
us_carrier_df.select(min($"distance"), max($"distance"), mean($"distance")).show()

// DataFrame.describe()
us_carrier_df.describe("distance").show()

// 중복값 제거
val us_carrier_1987_distinct_ds = us_carrier_1987_df.distinct()

// 정렬 후 출력
us_carrier_1987_distinct_ds.orderBy($"Origin", $"Dest").show()

// 전체 행 갯수
us_carrier_1987_distinct_ds.count()

// SQL 문으로 표현
spark.sql("""
  SELECT COUNT(DISTINCT(Origin, Dest)) AS Count 
    FROM global_temp.us_carrier 
   WHERE Year = 1987
""").show()

// 목적지 기준으로 그룹핑
val dest_by_arrival_groupedDS = us_carrier_df.groupBy($"Dest")

// row 카운팅
val dest_by_arrival_count_df = dest_by_arrival_groupedDS.count()

dest_by_arrival_count_df.show()

// 내림차순 정렬 메서드
import org.apache.spark.sql.functions.desc

dest_by_arrival_count_df.orderBy(desc("count")).show(10)
// same As
//dest_by_arrival_count_df.orderBy(-$"count").show(10)

// SQL 문으로 표현
spark.sql("""
  SELECT Dest, COUNT(*) AS Count 
    FROM global_temp.us_carrier 
   GROUP BY Dest 
   ORDER BY Count DESC LIMIT 10
""").show()
```

---

## 강의 링크: [PySpark를 활용한 데이터분석](https://www.edwith.org/sparktutorial)
edwith의 PySpark를 활용한 데이터분석 강좌의 내용 정리.

## Spark Doc 한글문서                                                 
[https://spark-korea.github.io/docs/](https://spark-korea.github.io/docs/)

---

# 전체 Code
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
