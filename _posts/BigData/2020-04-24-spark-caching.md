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
-- persist()메서드는 스토리지 레벨을 인자로 받아 저장 공간을 정할 수 있다.  
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

# 참고자료

링크: [databricks Cache()](https://databricks-prod-cloudfront.cloud.databricks.com/public/4027ec902e239c93eaaa8714f173bcfc/5211178207246023/950505630032626/7788830288800223/latest.html)  

Spark docs: [object StorageLevel](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.storage.StorageLevel$@MEMORY_ONLY_2:org.apache.spark.storage.StorageLevel)

Blog: [조대협님의 블로그 글](https://bcho.tistory.com/1029)
