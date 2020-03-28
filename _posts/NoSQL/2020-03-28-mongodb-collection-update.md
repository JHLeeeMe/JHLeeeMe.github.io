---
date: 2020-03-28 07:04:49
layout: post
title: "[MongoDB] db.collection.update() 정리"
subtitle:
description:
image: https://user-images.githubusercontent.com/31606119/77817441-286e0080-710e-11ea-924a-80c6656f18e0.png
optimized_image: https://user-images.githubusercontent.com/31606119/77817441-286e0080-710e-11ea-924a-80c6656f18e0.png
category: NoSQL
tags:
  - NoSQL
  - mongoDB
author: JHLeeeMe
paginate: false
---

### db.collection.updateOne(filter, update, options)
### db.collection.updateMany(filter, update, options)
### db.collection.replaceOne(filter, replacement, options)

## Syntax
```sql
db.collection.<updateOne|updateMany>(
   <filter>,
   <update>,
   {
     upsert: <boolean>,
     writeConcern: <document>,
     collation: <document>,
     arrayFilters: [ <filterdocument1>, ... ],
     hint:  <document|string>        // Available starting in MongoDB 4.2.1
   }
)
```

---

## 1. updateOne()
-- ```$currentDate```: lastModified필드를 생성 or 업데이트한다.
```sql
db.customers.insertOne(
   { _id: 1, status: "a", lastModified: ISODate("2013-10-02T01:11:18.965Z") }
)

db.customers.updateOne(
   { _id: 1 },
   {
     $currentDate: {
        lastModified: true,
        "cancellation.date": { $type: "timestamp" }
     },
     $set: {
        "cancellation.reason": "user request",
        status: "D"
     }
   }
)

db.customers.find().pretty()

--------------------------------------------------
Output:

{
   "_id" : 1,
   "status" : "D",
   "lastModified" : ISODate("2020-01-22T21:21:41.052Z"),
   "cancellation" : {
      "date" : Timestamp(1579728101, 1),
      "reason" : "user request"
   }
}
```

## 2. updateMany()
```sql
db.inventory.updateMany(
   { "qty": { $lt: 50 } },
   {
     $set: { "size.uom": "in", status: "P" },
     $currentDate: { lastModified: true }
   }
)
```

## 3. replaceOne()
-- document자체를 replace하기 때문에 ```$set```으로 감싸지 않는다.
```sql
db.test.insertMany([
    {"name" : "A", "Borough" : "Manhattan"},
    {"name" : "B", "Borough" : "Queens", "violations" : 2},
    {"name" : "B", "Borough" : "Brooklyn", "violations" : 0}
])


try {
   db.test.replaceOne(
      { "name" : "A" },
      { "name" : "K", "Borough" : "Manhattan" }
   );
} catch (e){
   print(e);
}

------------------------------------------------------------
Output:

{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }
```

-- ```upsert```: filter 조건에 매치되는 document가 없을 시 document가 추가된다.
```sql
{name: "Kim", age: 22}
{name: "Lee", age: 50}

db.collection.replaceOne(
    {name: "Choi"},
    {name: "Park"},
    {upsert: true}
)

-------------------------------
Output:

{name: "Kim", age: 22}
{name: "Lee", age: 50}
{name: "Park"}
```
