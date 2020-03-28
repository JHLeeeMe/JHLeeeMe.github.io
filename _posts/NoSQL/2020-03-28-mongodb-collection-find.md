---
date: 2020-03-28 05:55:36
layout: post
title: "[MongoDB] db.collection.find() 정리"
subtitle:
description:
image: https://user-images.githubusercontent.com/31606119/77815330-4c751600-70fd-11ea-8284-7ecae648ea28.png
optimized_image: https://user-images.githubusercontent.com/31606119/77815330-4c751600-70fd-11ea-8284-7ecae648ea28.png
category: NoSQL
tags:
  - NoSQL
  - mongoDB
author: JHLeeeMe
paginate: false
---

## db.collection.find(query, projection)

### 초기화  
```sql
db.test.insertMany([ 
    {
        "name"  : "Kim",
        "age"   : 22,
        "major" : "CS"},
    {
        "name"  : "Lee",
        "age"   : 24,
        "major" : "Japanense"},
    {
        "name"  : "Choi",
        "age"   : 44,
        "major" : "Korean"}
 ])
```

---

## 1. All
```sql
[mongo Shell]

  db.test.find({})


[MySQL]

  SELECT *
    FROM test;
```

## 2. Equal
```sql
[mongo Shell]

  db.test.find(
    {name: "Kim"},
    {_id: 0, name: 1, age: 1, major: 1}
  )


[MySQL]

  SELECT name, age, major
    FROM test
   WHERE name = "Kim";
```

## 3. In
```sql
[mongo Shell]

  db.test.find(
    {major: {$in: ["Korean", "japanese"]}}
  )


[MySQL]

  SELECT *
    FROM test
   WHERE major in ('Korean', 'japanese');
```

## 4. AND, OR, wildcard
```sql
[mongo Shell]

  db.test.find(
    {
      name: "A",
      $or: [{age: {$lt: 35}}, {major: /^J/}]
    }
  )


[MySQL]

  SELECT *
    FROM test
   WHERE name = 'A' and (age < 35 OR major LIKE 'J%');
```

## 참고) 비교문법
```bash
$eq     =    Matches values that are equal to a specified value.
$gt     >    Matches values that are greater than a specified value.
$gte    >=   Matches values that are greater than or equal to a specified value.
$in          Matches any of the values specified in an array.
$lt     <    Matches values that are less than a specified value.
$lte    <=   Matches values that are less than or equal to a specified value.
$ne     !=   Matches all values that are not equal to a specified value.
$nin         Matches none of the values specified in an array.
```

---

출처: [https://docs.mongodb.com/manual/tutorial/query-documents](https://docs.mongodb.com/manual/tutorial/query-documents/)
