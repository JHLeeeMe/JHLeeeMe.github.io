---
date: 2020-03-25 05:54:30
layout: post
title: "[프록래머스] 해시, 위장"
subtitle:
description:
image: https://user-images.githubusercontent.com/31606119/77444979-dab47800-6e2f-11ea-943e-40806166e093.jpg
optimized_image: https://user-images.githubusercontent.com/31606119/77444979-dab47800-6e2f-11ea-943e-40806166e093.jpg
category: Algo-and-DS
tags:
  - Algo-and-DS
author: JHLeeeMe
paginate: false
---

<a href='https://programmers.co.kr/'>
![programmers](https://user-images.githubusercontent.com/31606119/77444979-dab47800-6e2f-11ea-943e-40806166e093.jpg)
</a>

머리에 기름칠 좀 할 겸,  
매일 한 시간씩 프로그래머스에서 제공하는 코딩 테스트 연습문제를 풀고 있다.  
주소: [https://programmers.co.kr/learn/courses/30/parts/12077](https://programmers.co.kr/learn/courses/30/parts/12077)

---

오늘 푼 문제는 해시 문제
![0](https://user-images.githubusercontent.com/31606119/77506374-10497780-6ea9-11ea-8c43-713fcdf44535.png)
![1](https://user-images.githubusercontent.com/31606119/77506378-117aa480-6ea9-11ea-8561-f4a3fc0d5bef.png)
![2](https://user-images.githubusercontent.com/31606119/77506380-12133b00-6ea9-11ea-8403-921da3c2967e.png)

## 내 풀이
```python
from collections import Counter


def solution(clothes):
    result = Counter()
    for c in clothes:
        result += Counter([c[1]])

    answer = 1
    for e in list(result.values()):
        answer *= (e+1)

    return answer - 1
```
못 풀어서 다른 사람 풀이 댓글에  
```(각 의상 수 + 1)을 모두 곱하고 -1``` 하면 답이라는 걸 알았다.  
이를테면, 모자 2개 신발 1개 바지 3개 이면,  
경우의 수는 ```(2+1) * (1+1) * (3+1) - 1```  
그 아이디어로 코드는 직접 짜 봤다.  
```Counter()``` 파라미터로 String 값만 넣으면 문자 단위로 쪼개지므로  
```Counter([c[1]])``` 이 된 것. (단어 단위로 쪼개져야 하니깐)

## 가장 추천수가 높은 풀이
```python
def solution(clothes):
    from collections import Counter
    from functools import reduce
    cnt = Counter([kind for name, kind in clothes])
    answer = reduce(lambda x, y: x*(y+1), cnt.values(), 1) - 1
    return answer
```
함수형 프로그래밍의 ```fold``` 개념으로 접근한 풀이  

## fold  
```
재귀적인 자료구조를 분석, 주어진 결합 동작을 사용해
원 자료구조의 부분 구조를 반복적 처리 및 재결합해서 하나의 결괏값으로 반환하는 함수 집합
-Wikipedia
```

functools 모듈의 ```reduce()``` 가 그것이다.
![3](https://user-images.githubusercontent.com/31606119/77506971-8b5f5d80-6eaa-11ea-9edf-8da6da179d25.png)
