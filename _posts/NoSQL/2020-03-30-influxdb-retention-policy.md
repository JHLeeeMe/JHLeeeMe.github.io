---
date: 2020-03-30 07:52:33
layout: post
title: "[InfluxDB] Retention Policy란? & 설정 방법"
subtitle:
description: "influxDB의 retention policy는 무엇이고, 설정 방법은 어떻게 되는지 알아보자."
image: https://user-images.githubusercontent.com/31606119/77902496-3e212880-72bc-11ea-88a1-6b6355d632d7.jpg
optimized_image: https://user-images.githubusercontent.com/31606119/77902496-3e212880-72bc-11ea-88a1-6b6355d632d7.jpg
category: NoSQL
tags:
  - NoSQL
  - InfluxDB
author: JHLeeeMe
paginate: false
---

<a href="https://user-images.githubusercontent.com/31606119/77818265-6cfc9a80-7114-11ea-91b7-e9fcb0e0ab98.png">
![8](https://user-images.githubusercontent.com/31606119/77818265-6cfc9a80-7114-11ea-91b7-e9fcb0e0ab98.png)
</a>
이 전에 fluent-bit, influxDB, grafana로 시스템 모니터링 대시보드를 만든 적이 있다.  
내 글: [System-Monitoring-Dashboard 만들기(fluent-bit, influxDB, grafana)](https://jhleeeme.github.io/system-monitoring-dashboard/)

---

그런데 문제가 생겼다.  
시간이 갈수록 느려지고, 메모리를 많이 잡아먹더니  
이제는 grafana에 로딩도 안된다.  
influxDB에 데이터가 많이 쌓여서 그런가 생각했고, 그게 맞았나보다.  
grafana에서 select문을 전체 검색으로 해놔서 무리가 갔을 것이라고 생각된다.  
어떻게 할까 생각해면서 ```group by time()```을 알아보다가...  
어차피 현재 컴퓨터 상태를 보기 위해 만든 것이라 시간이 오래된 데이터들은 필요 없다고 판단했고,  
mongoDB의 capped collection(limited size collection)과 같은 것이 없을까 찾아봤다.  
그리고 그것이 ```Retention Policy``` 이다.

---

![timer](https://user-images.githubusercontent.com/31606119/77902496-3e212880-72bc-11ea-88a1-6b6355d632d7.jpg)
# Retention Policy
-- 설정된 시간이 지난 데이터들은 자동으로 만료(삭제) 시켜주는 정책  
-- 각 database마다 정의가 돼있다.  

먼저 ```influxdb.conf```를 보자.  
-- ```$ vi /etc/influxdb/influxdb.conf```
![0](https://user-images.githubusercontent.com/31606119/77889261-8fbfb800-72a8-11ea-977e-e9e50987bbe1.png)
![1](https://user-images.githubusercontent.com/31606119/77889264-91897b80-72a8-11ea-8dca-6a4247965ddf.png)

### default setting
-- database를 생성할 때 자동으로 retention policy를 생성하고  
ㄴ> ```(retention-autocreate = true)```  

-- retention policy 시행 사용 여부가 true이며 --> ```(enabled = true)```  

-- retention policy 시행 검사 시간 간격이 30분으로 돼있다.  
ㄴ> ```(check-interval = "30m")```

그러면 이제 database를 default로 생성 & retention policies를 보자.
```bash
$ Influx
  > create database test
  > use test
  > show retention policies
```
![2](https://user-images.githubusercontent.com/31606119/77889267-91897b80-72a8-11ea-813e-edba2908a4c6.png)

위 그림을 보면 ```"autogen"``` 이라는 이름으로 자동생성 돼있다.  
구조를 보자.  
```name```: retention policy name  
```duration```: influxDB가 data를 얼마나 보존하는가  
```shardGroupDuration```: shard group이 적용되는 시간 범위  
```replicaN```: 클러스터에 저장되는 data의 본가본 수  
```default```: 현재 database의 기본 정책 인가?

### retention policy를 다루는 방법
1. 자동으로 생성된 RP(retention policy)를 수정 (ALTER retention policy)
2. 자동 생성된 RP는 그대로 두고, 새로운 RP 생성 (CREATE retention policy)
3. database 생성 시점에서 정의 (CREATE database [database_name] WITE [RP_option])

---

### 1. 자동으로 생성된 RP 수정
```sql
ALTER RETENTION POLICY "policy_name" ON "db_name" retention_policy_option
                                                  [ retention_policy_option ]
                                                  [ retention_policy_option ]
                                                  [ retention_policy_option ]
```
-- ```duration``` 이 0s(infinite)로 돼있으므로 이를 바꿔보자.  
-- 주의할 점은 ```shardGroupDuration``` 보다 값이 커야한다.  
-- 기본으로 생성된 ```shardGroupDuration``` 은 1주일이다.  
-- 그러므로 ```duration``` 을 1주일 이상으로 설정하던지, ```shardGroupDuration``` 을 낮추던지 해야 함  
-- 아래 코드는 후자이다.
```sql
$ Influx
  > ALTER RETENTION POLICY "autogen" ON "test" DURATION 2h SHARD DURATION 1h
```
![3](https://user-images.githubusercontent.com/31606119/77889270-92baa880-72a8-11ea-97ba-a312d2af8a0a.png)

### 2. 자동 생성된 RP는 그대로 두고 새로운 RP생성
```sql
CREATE RETENTION POLICY "policy_name" ON "db_name" duration_option
                                                   replication_option
                                                   [ shard_group_duration_option ]
                                                   [ "DEFAULT" ]
```
```sql
$ influx
  -- Create a retention policy.
  > CREATE RETENTION POLICY "two_hours" ON "test" DURATION 2h REPLICATION 1

  -- Create a retention policy and set it as the DEFAULT.
  > CREATE RETENTION POLICY "one_hour" ON "test" DURATION 60m REPLICATION 1 DEFAULT

  -- Create a retention policy and specify the shard group duration.
  > CREATE RETENTION POLICY "one_hour_shard_30m" ON "test" DURATION 1h REPLICATION 1 SHARD DURATION 30m
```
![4](https://user-images.githubusercontent.com/31606119/77889273-92baa880-72a8-11ea-861e-fabc9d66d636.png)
![5](https://user-images.githubusercontent.com/31606119/77889274-93533f00-72a8-11ea-9382-6d1195c62453.png)

-- ```retention policy``` 들이 생성되었으며,  
-- test 데이터베이스는 ```"one_hour"``` 라는 이름의 RP를 기본으로 한다.  
-- 그런데 ```shardGroupDuration```은 30분으로 설정해도 1시간으로 설정됐다.  
-- 이건 마지막에 다시 언급하겠다.

### 3. database 생성 시점에서 정의
```sql
CREATE DATABASE db_name [ WITH [ duration_option ]
                               [ replication_option ]
                               [ shard_group_duration_option ]
                               [ retention_policy_name ]]
```
```sql
$ influx
  -- Create a database called rp_test0 with a new DEFAULT retention policy and specify the duration, replication, shard group duration, and name of that retention policy
  > CREATE DATABASE "rp_test0" WITH DURATION 1d REPLICATION 1 SHARD DURATION 30m NAME "one_day_shard_30m"

  -- Create a database called rp_test1 with a new DEFAULT retention policy and specify the name of that retention policy
  > CREATE DATABASE "rp_test1" WITH NAME "wow"
```
![6](https://user-images.githubusercontent.com/31606119/77889276-93ebd580-72a8-11ea-8bad-c510e00bddd6.png)
-- ```rp_test0```, ```rp_test1``` 데이터베이스가 생성되었다.

![7](https://user-images.githubusercontent.com/31606119/77889279-94846c00-72a8-11ea-9b7b-8eb4a68b6cec.png)
![8](https://user-images.githubusercontent.com/31606119/77889283-951d0280-72a8-11ea-9312-92d603dd28b1.png)
-- retention policies를 보면 자동 생성되던 ```"autogen"``` 이라는 RP가 ALTER 되었다는 걸 알 수 있다.

---

글을 다 작성하고 보니 ```shardGroupDuration```의 최솟값은 ```1h``` 인가 보다...  
```duration``` 은 에러 메세지가 나와서 알고 있었는데 ```shardGroupDuration``` 은 에러메세지가 안나온다.  

```결론은 shardGroupDuration의 최솟값 ==  1h```  
```duration의 최솟값 == shardGroupDuration의 값과 같은 값 (즉, 최소 설정 가능 값은 1h)```

---

## 참고자료
Blog: [simplifying-influxdb-retention-policy-best-practices](https://www.influxdata.com/blog/simplifying-influxdb-retention-policy-best-practices/)

Doc: [https://docs.influxdata.com/influxdb/v1.2/query_language/spec/#alter-retention-policy](https://docs.influxdata.com/influxdb/v1.2/query_language/spec/#alter-retention-policy)

