---
date: 2020-03-27 06:39:58
layout: post
title: "[프로그래머스] 스택/큐, 탑"
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
주소: [https://programmers.co.kr/learn/courses/30/parts/12081](https://programmers.co.kr/learn/courses/30/parts/12081)

---

오늘 푼 문제는 스택/큐 문제
![0](https://user-images.githubusercontent.com/31606119/77729646-89c89d80-7042-11ea-9233-237f93d7291b.png)
![1](https://user-images.githubusercontent.com/31606119/77729649-8af9ca80-7042-11ea-8124-52fda7e12772.png)
![2](https://user-images.githubusercontent.com/31606119/77729650-8b926100-7042-11ea-86f9-0490c9eaffe7.png)

## 내 풀이
```python
def solution(heights):
    answer = []

    while True:
        num = len(heights)
        send = heights.pop()

        if heights:
            for reception in reverse(heights):
                if send < reception:
                    answer.append(num-1)
                    break

                num -= 1

                if num == 1:
                    answer.append(0)
                    break
        else:
            answer.append(0)
            return reverse(answer)


def reverse(obj_list):
    obj_list_reversed = []
    for i in obj_list:
        obj_list_reversed.insert(0, i)
    return obj_list_reversed
```
너무 지저분하다. ```pop()```을 쓰긴 썼다.  
```reverse()```가 안먹혀서 함수를 간단하게 만들었다.

## 추천수가 가장 많은 풀이
```python
def solution(h):
    ans = [0] * len(h)
    for i in range(len(h)-1, 0, -1):
        for j in range(i-1, -1, -1):
            if h[i] < h[j]:
                ans[i] = j+1
                break
    return ans
```
range에 step을 활용한 풀이. 신박하진 않다.
