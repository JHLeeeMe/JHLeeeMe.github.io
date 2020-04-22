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

-- UDF (User Define Function)  

-- DataFrame.```drop()```  
-- DataFrame.```withColumn()```  

-- cache()  

-- DataFrame.```createOfReplaceGlobalTempView()```  

-- SparkSession.```sql()```  
-- DataFrame.```select()```  
-- spark.sql.functions.```col()```  
-- DataFrame.```filter()```  
-- DataFrame.```distinct()```  
-- DataFrame.```orderBy()```  
-- spark.sql.functions.```{min, max, mean}```  
-- spark.sql.functions.```desc()```  
-- DataFrame.```groupBy()```  
-- DataFrame.```join()```  
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
//save As
//val df = spark.read.format("json").load("<path>")

val df2 = spark.read
  .option("header", "true")
  .option("inferSchema", "true")
  .csv("<path>")
//save As
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
-- sql문을 톨한 조회작업을 DataFrame API를 사용해 처리할 수 있다.  
-- 아래 목록들을 sql문과 비교해보자.
-- SparkSession.```sql()```  
-- 1. DataFrame.```select()```  
-- 2. DataFrame.```filter()```  
-- 3. DataFrame.```distinct()```  
-- 4. DataFrame.```orderBy()```  
-- 5. DataFrame.```groupBy()```  
-- 6. DataFrame.```join()```  
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
---

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

## 강의 링크: [PySpark를 활용한 데이터분석](https://www.edwith.org/sparktutorial)
edwith의 PySpark를 활용한 데이터분석 강좌의 내용 정리.

## Spark Doc 한글문서                                                 
[https://spark-korea.github.io/docs/](https://spark-korea.github.io/docs/)
