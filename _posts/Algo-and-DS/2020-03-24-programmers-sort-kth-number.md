---
date: 2020-03-24 14:57:21
layout: post
title: "[프로그래머스] 정렬, K번째 수"
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
주소: [https://programmers.co.kr/learn/courses/30/parts/12198](https://programmers.co.kr/learn/courses/30/parts/12198)

---

오늘 푼 문제는 정렬문제  
![0](https://user-images.githubusercontent.com/31606119/77441262-94a9e500-6e2c-11ea-9f7b-c567b817a09b.png)
![1](https://user-images.githubusercontent.com/31606119/77441268-95db1200-6e2c-11ea-8f4a-9ee4c47d6ea3.png)
![2](https://user-images.githubusercontent.com/31606119/77441273-970c3f00-6e2c-11ea-8afc-0ff67c32b3d3.png)


## 내 풀이
```python
def solution(array, commands):
    answer = []
    for c in commands:
        target = array[c[0]-1:c[1]]
        sorted_array = sorted(target)
        answer.append(sorted_array[c[2]-1])

    return answer
```
풀이를 할 때 2차원 리스트를 foreach로 돌리면 어떻게 나오는지 까먹었었다.

## 가장 추천수가 높은 풀이
```python
def solution(array, commands):
    return list(map(lambda x:sorted(array[x[0]-1:x[1]])[x[2]-1], commands))
```
map과 람다식에 익숙해져야겠다. 그다지 맘에 드는 코드는 아니지만.  
나머지 풀이는 내 풀이와 비슷했다.

## 가장 좋았던 풀이
```python
def solution(array, commands):
    answer = []
    for command in commands:
        i,j,k = command  # 이 부분
        answer.append(list(sorted(array[i-1:j]))[k-1])
    return answer
```
변수에 리스트 값들을 한 번에 집어넣는다는 발상이 스무스하지가 않다.  
익숙해지자. zip()도  

프로그래머스에서 본 문제 중에 가장 쉬웠다.
