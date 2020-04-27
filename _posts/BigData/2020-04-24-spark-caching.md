---
date: 2020-04-24 03:14:07
layout: post
title: "[Spark] cache()와 persist()의 차이"
subtitle:
description:
image: https://user-images.githubusercontent.com/31606119/80176036-56256700-8632-11ea-8311-fb310273efa6.jpg
optimized_image: https://user-images.githubusercontent.com/31606119/80176036-56256700-8632-11ea-8311-fb310273efa6.jpg
category: BigData
tags:
  - BigData
  - Spark
author: JHLeeeMe
paginate: false
---

![Spark](https://user-images.githubusercontent.com/31606119/80176036-56256700-8632-11ea-8311-fb310273efa6.jpg)

# cache()와 persist()의 차이
Spark로 데이터를 다룰 때 ```Action```수행 시점마다 로드되지 않고,  
한번 로드한 데이터를 메모리상에 상주 시키는 메서드가 있으며,  
그것이 ```cache()```와 ```persist()```이다.  
둘의 차이를 알아보자.

## 1. 먼저 cache()
```scala
scala> val test = Seq(1, 2, 3)
test: Seq[Int] = List(1, 2, 3)

// to DF
scala> val test_df = test.toDF()
test_df: org.apache.spark.sql.DataFrame = [value: int]

// 캐싱
scala> test_df.cache()

scala> test_df.storageLevel
res0: org.apache.spark.storage.StorageLevel = StorageLevel(disk, memory, deserialized, 1 replicas)
```

## 2. persist()
-- ```persist()```메서드는 스토리지 레벨을 인자로 받아 저장 공간을 정할 수 있다.  
```scala
scala> val test2 = Seq(1, 2, 3)
scala> val test2_df = test2.toDF()

scala> import org.apache.spark.storage.StorageLevel

// 캐싱
scala> test2_df.persist(StorageLevel.MEMORY_AND_DISK)

scala> test2_df.storageLevel
res1: org.apache.spark.storage.StorageLevel = StorageLevel(disk, memory, deserialized, 1 replicas)
```

## 3. Storage Level
-- 스토리지 레벨에는 여러 종류가 있다.
![spark_storage_level](https://user-images.githubusercontent.com/31606119/80176030-50c81c80-8632-11ea-92c7-6f34f9b7f716.png)
-- ```~_SER```은 원본이 아닌 Serealize 된 형태로 저장한다는 것, 저장용량에서의 이점은 있지만, 저장할 때와 읽을 때 SER, deSER로 인한 CPU사용량 증가가 있다.

-- DF.```storageLevel```메서드와 RDD.```getStorageLevel```을 사용해 스토리지 레벨을 확인할 수 있다.  
아래 코드는 ```DF```와 ```RDD```에서 cache()메서드 기본값의 차이를 보여준다.
```scala
scala> import org.apache.spark.storage.StorageLevel

scala> val test3 = Seq(1, 2, 3)
scala> val test3_df = test3.toDF()

// RDD & DF 스토리지 레벨
scala> test3_df.rdd.getStorageLevel
res0: org.apache.spark.storage.StorageLevel = StorageLevel(1 replicas)
scala> test3_df.storageLevel
res1: org.apache.spark.storage.StorageLevel = StorageLevel(1 replicas)

// DF 캐싱
scala> test_df.cache()

// RDD & DF 스토리지 레벨
scala> test3_df.rdd.getStorageLevel
res3: org.apache.spark.storage.StorageLevel = StorageLevel(1 replicas)
scala> test3_df.storageLevel
res4: org.apache.spark.storage.StorageLevel = StorageLevel(disk, memory, deserialized, 1 replicas)

// RDD 캐싱
scala> test3_df.rdd.cache()

// RDD 스토리지 레벨
scala> test3_df.rdd.getStorageLevel
org.apache.spark.storage.StorageLevel = StorageLevel(memory, deserialized, 1 replicas)
```
위 코드에서 보이듯이 ```cache()```메서드는  
```RDD```에선 persist(StorageLevel.```MEMORY_ONLY```)  
```DF```에선 persist(StorageLevel.```MEMORY_AND_DISK```) 로 작동한다.

## 4. 언캐싱
-- 캐싱의 반대  
-- ```unpersist()```메서드를 쓰며 ```DF```와 ```RDD```는 따로 관리된다.
```scala
// DF 생성
scala> val test4 = Seq(1, 2, 3)
scala> val test4_df = test4.toDF()

// RDD & DF 캐싱
scala> test4_df.rdd.cache()
scala> test4_df.cache()

// RDD & DF 스토리지 레벨 확인
scala> test4_df.rdd.getStorageLevel
res3: org.apache.spark.storage.StorageLevel = StorageLevel(memory, deserialized, 1 replicas)
scala> test4_df.storageLevel
res4: org.apache.spark.storage.StorageLevel = StorageLevel(disk, memory, deserialized, 1 replicas)

// DF 언캐싱
scala> test4_df.unpersist()

// RDD & DF 스토리지 레벨 확인
scala> test4_df.rdd.getStorageLevel
res6: org.apache.spark.storage.StorageLevel = StorageLevel(memory, deserialized, 1 replicas)
scala> test4_df.storageLevel
res7: org.apache.spark.storage.StorageLevel = StorageLevel(1 replicas)

// RDD 언캐싱
scala> test4_df.rdd.unpersist()

// RDD & DF 스토리지 레벨 확인
scala> test4_df.rdd.getStorageLevel
res9: org.apache.spark.storage.StorageLevel = StorageLevel(1 replicas)
scala> test4_df.storageLevel
res10: org.apache.spark.storage.StorageLevel = StorageLevel(1 replicas)
```

# 결론
```cache()```메서드와 ```persist()```메서드는 둘 다 로드된 데이터를 저장공간상에 올려두는 작업을 하며,  
```RDD.cache()```는 persist(StorageLevel.```MEMORY_ONLY```)  
```DF.cache()```는 persist(StorageLevel.```MEMORY_AND_DISK```) 로 작동한다.  

즉, ```val df = Seq(1, 2, 3).toDF()```라는 DataFrame이 있을 때  
```df.cache()```는 ```df.persist(StorageLevel.MEMORY_AND_DISK)```와 같다고 볼 수 있다.

---

# 추가) Temp View 캐싱에 관하여
Spark에서 sql문으로 작업을 하기 위해 Temp View를 생성한다.  
Temp View도 캐싱이 가능하다.  
이전에 [Temp View 관련 글](https://jhleeeme.github.io/spark-temp-view/#3-temp-view-%EC%82%AD%EC%A0%9C-%EB%A9%94%EC%84%9C%EB%93%9C)에서 Temp View 삭제 메서드를 쓸 때  
```spark.catalog.dropTempView()```메서드를 사용했다.  
그렇다. Temp View나 Table등을 관리하는 메서드들은 ```spark.catalog```에 모아져있다.  
```spark.catalog```를 살펴보자.

## Catalog
![spark_catalog](https://user-images.githubusercontent.com/31606119/80333807-3e482000-888a-11ea-9207-05e321e27b4a.png)
여러 메서드들 중에 간단히 몇가지만 살펴보겠다. 자세한 내용은 [여기](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.catalog.Catalog)  
-- ```cacheTable, uncacheTable``` : View 캐싱, 언캐싱  
-- ```dropTempView, dropGlobalTempView``` : Drop View & Global View  
-- ```isCached``` : 캐싱 상태확인  
-- ```clearCache``` : 모든 View 언캐싱  
-- ```tableExists``` : View 존재확인  
위 메서드들 말고도 테이블이나 칼럼의 리스트를 본다거나 존재여부 등을 체크하는 메서드들이 있다.

아래는 간단한 DF의 TempView를 만들어서 캐싱 해보았다.
```scala
// DF 생성
scala> val test1_df = Seq(1, 2, 3).toDF()

// View 생성
scala> test1_df.createTempView("test1_tempView")

// DF & Temp View 스토리지 레벨 체크
scala> test1_df.storageLevel
scala> spark.catalog.isCached("test1_tempView")
resN: org.apache.spark.storage.StorageLevel = StorageLevel(1 replicas)
resN: Boolean = false

// Temp View Caching
scala> spark.catalog.cacheTable("test1_tempView")

// DF & Temp View 스토리지 레벨 체크
scala> test1_df.storageLevel
scala> spark.catalog.isCached("test1_tempView")
resN: org.apache.spark.storage.StorageLevel = StorageLevel(disk, memory, deserialized, 1 replicas)
resN: Boolean = true
```
위 코드에서 뭔가 이상한게 하나있다.  
```Temp View```만 캐싱했는데 ```DF```까지 캐싱이 되었다.  
DF와 그 DF로 생성한 View, Table 등은 ```스토리지 레벨을 공유한다.```  
즉, 위 코드에서 DF나 TempView를 언캐싱하면 둘 다 언캐싱 된다는 소리다.  
아래 코드는 위 코드의 ```test1_tempView```를 언캐싱해보겠다.
```scala
#!/usr/bin/env spark-shell
// DF 언캐싱
scala> spark.catalog.uncacheTable("test1_tempView")

// DF & Temp View 스토리지 레벨 체크
scala> test1_df.storageLevel
scala> spark.catalog.isCached("test1_tempView")
resN: org.apache.spark.storage.StorageLevel = StorageLevel(1 replicas)
resN: Boolean = false
```
예상대로이다.  
```test1_df.unpersist()```를 (또는 ```spark.catalog.clearCache()```) 실행해도 결과는 마찬가지  
그렇다면 기존 DF가 캐싱이 된 상태에서 View를 생성한다면?  
View와 DF는 스토리지 레벨을 공유하므로 생성된 View도 캐싱이 자동으로 될것이다.  
확인해보자.
```scala
// DF 캐싱 상태 확인
scala> test1_df.storageLevel
resN: org.apache.spark.storage.StorageLevel = StorageLevel(1 replicas)

// DF 캐싱
scala> test1_df.cache()
resN: test1_df.type = [value: int]

// View 생성
scala> test1_df.createTempView("abc")

// View 캐싱 상태 확인
scala> spark.catalog.isCached("abc")
resN: Boolean = true
```
예상대로이다.  
그렇다면 또 다른 궁금증.  
현재 View가 ```test1_tempView```와 ```abc```가 있는데 여기서 하나의 View를 언캐싱 한다면??  
두 View 모두 같은 ```test1_df```로 만들어졌으므로 하나의 View를 언캐싱하면 DF도 언캐싱되고  
그것과 스토리지레벨을 공유하는 또다른 View도 언캐싱될것으로 예상된다. 살펴보자.
```scala
scala> spark.catalog.listTables().show()
+--------------+--------+-----------+---------+-----------+
|          name|database|description|tableType|isTemporary|
+--------------+--------+-----------+---------+-----------+
|test1_tempview|    null|       null|TEMPORARY|       true|
|           abc|    null|       null|TEMPORARY|       true|
+--------------+--------+-----------+---------+-----------+

// DF & Views 스토리지 레벨 체크
scala> test1_df.storageLevel
scala> spark.catalog.isCached("test1_tempView")
scala> spark.catalog.isCached("abc")
resN: org.apache.spark.storage.StorageLevel = StorageLevel(disk, memory, deserialized, 1 replicas)
resN: Boolean = true
resN: Boolean = true

// 하나의 View 언캐싱
scala> spark.catalog.uncacheTable("abc")

// DF & Views 스토리지 레벨 체크
scala> test1_df.storageLevel
scala> spark.catalog.isCached("test1_tempView")
scala> spark.catalog.isCached("abc")
resN: org.apache.spark.storage.StorageLevel = StorageLevel(1 replicas)
resN: Boolean = false
resN: Boolean = false
```
예상대로이다.

이번엔 View를 Drop해보자.  
```scala
// View 존재 확인
scala> spark.catalog.tableExists("test1_tempView")
resN: Boolean = true

// Drop View
scala> spark.catalog.dropTempView("test1_tempView")
resN: Boolean = true

// View 존재 확인
scala> spark.catalog.tableExists("test1_tempView")
resN: Boolean = false

scala> spark.catalog.listTables().show()
+--------------+--------+-----------+---------+-----------+
|          name|database|description|tableType|isTemporary|
+--------------+--------+-----------+---------+-----------+
|           abc|    null|       null|TEMPORARY|       true|
+--------------+--------+-----------+---------+-----------+
```

---

# 참고자료

링크: [databricks Cache()](https://databricks-prod-cloudfront.cloud.databricks.com/public/4027ec902e239c93eaaa8714f173bcfc/5211178207246023/950505630032626/7788830288800223/latest.html)  

Spark docs: [object StorageLevel](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.storage.StorageLevel$@MEMORY_ONLY_2:org.apache.spark.storage.StorageLevel)

Spark docs: [abstract class Catalog](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.catalog.Catalog)

Blog: [조대협님의 블로그 글](https://bcho.tistory.com/1029)
