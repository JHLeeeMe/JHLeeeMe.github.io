---
date: 2020-05-21 12:24:31
layout: post
title: "[Pandas] DataFrame 병합 메서드(concat(), merge())"
subtitle:
description:
image: https://user-images.githubusercontent.com/31606119/82567334-309b6700-9bb8-11ea-900f-b104f9248807.png
optimized_image: https://user-images.githubusercontent.com/31606119/82567334-309b6700-9bb8-11ea-900f-b104f9248807.png
category: Python
tags: 
  - Python
  - Pandas
author: JHLeeeMe
paginate: false
---
![pandas-merging](https://user-images.githubusercontent.com/31606119/82567334-309b6700-9bb8-11ea-900f-b104f9248807.png)

# DataFrame 병합하기
-- 1. pd.concat()  
-- 2. pd.merge()

먼저 연습용 DataFrame을 3개 생성해보자.
```java
>>> import pandas as pd

>>> df1 = pd.DataFrame([['A0', 'A1', 'A2', 'A3'],
...                     ['B0', 'B1', 'B2', 'B3'],
...                     ['C0', 'C1', 'C2', 'C3'],
...                     ['D0', 'D1', 'D2', 'D3']], columns=list('ABCD'))
 
>>> df2 = pd.DataFrame([['A4', 'A5', 'A6', 'A7'],
...                     ['B4', 'B5', 'B6', 'B7'],
...                     ['C4', 'C5', 'C6', 'C7'],
...                     ['D4', 'D5', 'D6', 'D7']], columns=['A', 'B', 'C', 'D'], index=[4, 5, 6, 7])

>>> df3 = pd.DataFrame([['A8', 'A9', 'A10', 'A11'],
...                     ['B8', 'B9', 'B10', 'B11'],
...                     ['C8', 'C9', 'C10', 'C11'],
...                     ['D8', 'D9', 'D10', 'D11']], columns=list('ABCD'), index=[8, 9, 10, 11])

>>> df1
    A   B   C   D
0  A0  A1  A2  A3
1  B0  B1  B2  B3
2  C0  C1  C2  C3
3  D0  D1  D2  D3

>>> df2
    A   B   C   D
4  A4  A5  A6  A7
5  B4  B5  B6  B7
6  C4  C5  C6  C7
7  D4  D5  D6  D7

>>> df3
     A   B    C    D
8   A8  A9  A10  A11
9   B8  B9  B10  B11
10  C8  C9  C10  C11
11  D8  D9  D10  D11
```

## 1. pd.concat()
-- Pandas doc: [Pandas.concat](https://pandas.pydata.org/docs/reference/api/pandas.concat.html)  
-- 살펴볼 옵션들: ```axis```, ```join_axes```, ```ignore_index```

열 방향 병합이 default
```java
>>> pd.concat([df1, df2, df3])
     A   B    C    D
0   A0  A1   A2   A3
1   B0  B1   B2   B3
2   C0  C1   C2   C3
3   D0  D1   D2   D3
4   A4  A5   A6   A7
5   B4  B5   B6   B7
6   C4  C5   C6   C7
7   D4  D5   D6   D7
8   A8  A9  A10  A11
9   B8  B9  B10  B11
10  C8  C9  C10  C11
11  D8  D9  D10  D11
```

-- ```axis=1``` 옵션을 통한 행 방향 병합
```java
>>> pd.concat([df1, df2, df3], axis=1)
      A    B    C    D    A    B    C    D    A    B    C    D
0    A0   A1   A2   A3  NaN  NaN  NaN  NaN  NaN  NaN  NaN  NaN
1    B0   B1   B2   B3  NaN  NaN  NaN  NaN  NaN  NaN  NaN  NaN
2    C0   C1   C2   C3  NaN  NaN  NaN  NaN  NaN  NaN  NaN  NaN
3    D0   D1   D2   D3  NaN  NaN  NaN  NaN  NaN  NaN  NaN  NaN
4   NaN  NaN  NaN  NaN   A4   A5   A6   A7  NaN  NaN  NaN  NaN
5   NaN  NaN  NaN  NaN   B4   B5   B6   B7  NaN  NaN  NaN  NaN
6   NaN  NaN  NaN  NaN   C4   C5   C6   C7  NaN  NaN  NaN  NaN
7   NaN  NaN  NaN  NaN   D4   D5   D6   D7  NaN  NaN  NaN  NaN
8   NaN  NaN  NaN  NaN  NaN  NaN  NaN  NaN   A8   A9  A10  A11
9   NaN  NaN  NaN  NaN  NaN  NaN  NaN  NaN   B8   B9  B10  B11
10  NaN  NaN  NaN  NaN  NaN  NaN  NaN  NaN   C8   C9  C10  C11
11  NaN  NaN  NaN  NaN  NaN  NaN  NaN  NaN   D8   D9  D10  D11
```

join 옵션을 살펴보기 위해  
데이터 프레임을 하나 더 만들어서 살펴보자.
```java
>>> df4 = pd.DataFrame({'B': ['B2', 'B3', 'B6', 'B7'],
...                     'D': ['D2', 'D3', 'D6', 'D7'],
...                     'F': ['F2', 'F3', 'F6', 'F7']}, index=[2, 3, 6, 7])

>>> df4
    B   D   F
2  B2  D2  F2
3  B3  D3  F3
6  B6  D6  F6
7  B7  D7  F7
```

-- ```join``` 옵션  
'outer'가 default
```java
>>> pd.concat([df1, df4], axis=1)
     A    B    C    D    B    D    F
0   A0   A1   A2   A3  NaN  NaN  NaN
1   B0   B1   B2   B3  NaN  NaN  NaN
2   C0   C1   C2   C3   B2   D2   F2
3   D0   D1   D2   D3   B3   D3   F3
6  NaN  NaN  NaN  NaN   B6   D6   F6
7  NaN  NaN  NaN  NaN   B7   D7   F7

>>> pd.concat([df1, df4], axis=1, join='outer')
     A    B    C    D    B    D    F
0   A0   A1   A2   A3  NaN  NaN  NaN
1   B0   B1   B2   B3  NaN  NaN  NaN
2   C0   C1   C2   C3   B2   D2   F2
3   D0   D1   D2   D3   B3   D3   F3
6  NaN  NaN  NaN  NaN   B6   D6   F6
7  NaN  NaN  NaN  NaN   B7   D7   F7

>>> pd.concat([df1, df4], axis=1, join='inner')
    A   B   C   D   B   D   F
2  C0  C1  C2  C3  B2  D2  F2
3  D0  D1  D2  D3  B3  D3  F3
```

-- ```join_axes``` 옵션  
특정 DataFrame의 인덱스만을 취할 수 있다. (left join like)
```java
>>> pd.concat([df1, df4], axis=1, join_axes=[df1.index])
    A   B   C   D    B    D    F
0  A0  A1  A2  A3  NaN  NaN  NaN
1  B0  B1  B2  B3  NaN  NaN  NaN
2  C0  C1  C2  C3   B2   D2   F2
3  D0  D1  D2  D3   B3   D3   F3
```

-- ```ignore_index``` 옵션   
인덱스를 무시하고 병합 후, 다시 인덱스를 부여함.
```java
>>> pd.concat([df1, df4], ignore_index=True)
     A   B    C   D    F
0   A0  A1   A2  A3  NaN
1   B0  B1   B2  B3  NaN
2   C0  C1   C2  C3  NaN
3   D0  D1   D2  D3  NaN
4  NaN  B2  NaN  D2   F2
5  NaN  B3  NaN  D3   F3
6  NaN  B6  NaN  D6   F6
7  NaN  B7  NaN  D7   F7
```

## 2. pd.merge()
-- Pandas doc: [Pandas.merge](https://pandas.pydata.org/docs/reference/api/pandas.merge.html)  
-- 공통 칼럼을 기준으로 join가능  
-- 살펴볼 옵션들: ```on```, ```how```  

먼저 연습용 데이터 생성
```java
>>> left = pd.DataFrame({'key': ['K0', 'K4', 'K2', 'K3'],
...                      'A': ['A0', 'A4', 'A2', 'A3'],
...                      'B': ['B0', 'B4', 'B2', 'B3']})

>>> right = pd.DataFrame({'key': ['K0', 'K1', 'K2', 'K3'],
...                       'C': ['C0', 'C1', 'C2', 'C3'],
...                       'D': ['D0', 'D1', 'D2', 'D3']})

>>> left
    A   B key
0  A0  B0  K0
1  A4  B4  K4
2  A2  B2  K2
3  A3  B3  K3

>>> right
    C   D key
0  C0  D0  K0
1  C1  D1  K1
2  C2  D2  K2
3  C3  D3  K3
```

공통 칼럼인 'key'를 기준으로 join 해보자.  
```on``` 옵션을 통해 기준을 정할 수 있다.  
```how``` 옵션은 'inner'가 default
```java
>>> pd.merge(left, right, on='key')
    A   B key   C   D
0  A0  B0  K0  C0  D0
1  A2  B2  K2  C2  D2
2  A3  B3  K3  C3  D3

>>> pd.merge(left, right, on='key', how='inner')
    A   B key   C   D
0  A0  B0  K0  C0  D0
1  A2  B2  K2  C2  D2
2  A3  B3  K3  C3  D3

>>> pd.merge(left, right, on='key', how='outer')
     A    B key    C    D
0   A0   B0  K0   C0   D0
1   A4   B4  K4  NaN  NaN
2   A2   B2  K2   C2   D2
3   A3   B3  K3   C3   D3
4  NaN  NaN  K1   C1   D1

>>> pd.merge(left, right, on='key', how='left')
    A   B key    C    D
0  A0  B0  K0   C0   D0
1  A4  B4  K4  NaN  NaN
2  A2  B2  K2   C2   D2
3  A3  B3  K3   C3   D3

>>> pd.merge(left, right, on='key', how='right')
     A    B key   C   D
0   A0   B0  K0  C0  D0
1   A2   B2  K2  C2  D2
2   A3   B3  K3  C3  D3
3  NaN  NaN  K1  C1  D1
```
