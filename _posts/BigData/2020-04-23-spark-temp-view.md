---
date: 2020-04-23 11:50:37
layout: post
title: "[Spark] Temp View 생성 및 삭제"
subtitle:
description:
image: https://user-images.githubusercontent.com/31606119/80107640-4d8f4b00-85b6-11ea-949b-ae0e6b34b76a.jpg
optimized_image: https://user-images.githubusercontent.com/31606119/80107640-4d8f4b00-85b6-11ea-949b-ae0e6b34b76a.jpg
category: BigData
tags:
  - BigData
  - Spark
author: JHLeeeMe
paginate: false
---

![Spark](https://user-images.githubusercontent.com/31606119/80107640-4d8f4b00-85b6-11ea-949b-ae0e6b34b76a.jpg)

# Temp View 생성 및 삭제하기
-- spark 2.x 버전 기준으로 작성

## 1. Temp View란?
-- SQL의 그것, View (Table 말고)  
Spark내에서 데이터 분석작업을 할 때  
```DataFrame API```말고, 친근한 SQL문으로 데이터를 다룰 수 있다.  
```SparkSession.sql()```메서드를 통해서 말이다. 어떻게 사용되는지 아래 예를 보자.
```scala
// DataFrame API
df.select($"column_name1", $"column_name2").show()

// SQL
df.createOrReplaceTempView("df_view")  // Temp View 생성
spark.sql("SELECT column_name1, column_name2 FROM df_view").show()
```
위 두 가지 방법으로 작성된 코드는 같은 작업을 한다.  
```sql()```메서드도 컴파일 과정에서 ```DataFrame API```꼴로 변환되니 같다고 볼 수 있겠다.  
둘의 차이점은 sql문으로 작업을 하기위해선 df의 View를 생성해야하고,  
그것이 ```df.createOrReplaceTempView("<view_name>")``` 요 부분이다.

## 2. Temp View 생성 메서드
-- DataFrame.```createTempView()```  
-- DataFrame.```createGlobalTempView()```  
-- DataFrame.```createOrReplaceTempView()```  
-- DataFrame.```createOrReplaceGlobalTempView()```  
-- Spark Doc: [DataSet[T]](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.Dataset)  

차근차근 살펴보자.  
일단 크게 ```create~```꼴과 ```createOrReplace~```꼴로 나뉜다.  
메서드 이름에서 어떤 차이인지 파악이 가능하므로 넘어가겠다.  
그 다음 ```~TempView()```와 ```~GlobalTempView()```를 살펴보자.

### 2-1. 일단 TempView
-- Local Temporary View  
-- ```Session Scope```, (i.e. 세션이 종료되면 drop된다.)  
-- 아래는 TempView 생성 예
```scala
df.createTempView("df_view")
df.createOrReplaceTempView("df_view")

spark.sql("SELECT * FROM df_view")
```

### 2-2. GlobalTempView
-- Global Temporary View  
-- ```Cross-Session```, (i.e. 세션간 공유, spark 어플리케이션이 종료되면 drop)  
-- 아래는 GlobalTempView 생성 예
```scala
df.createGlobalTempView("df_globalView")
df.createOrReplaceGlobalTempView("df_globalView")

spark.sql("SELECT * FROM global_temp.df_globalView")
```
```GlobalTempView```는 시스템의 임시 디비로 연결되므로(e.g. Master Node의 하드디스크)  
접근할때 ```global_temp```로 접근

## 3. Temp View 삭제 메서드
-- 3-1. spark.catalog.```dropTempView()```  
-- 3-2. spark.catalog.```dropGlobalTempView()```  
-- Spark Doc: [Catalog](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.catalog.Catalog)

간단하다. 코드로 보자.
```scala
spark.catalog.dropTempView("df_view")
spark.catalog.dropGlobalTempView("df_globalView")
```

다음엔 캐싱에 관한 이야기를 좀 해보고자 한다. ㅎ

---

# 참고자료

[Spark 2.3 Dropping Temp Table [duplicate] - apache-spark](https://html.developreference.com/article/11971626/Spark+2.3+Dropping+Temp+Table+%5Bduplicate%5D)
