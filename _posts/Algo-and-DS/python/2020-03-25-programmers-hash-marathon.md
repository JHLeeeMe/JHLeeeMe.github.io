---
date: 2020-03-25 05:08:51
layout: post
title: "[프로그래머스] 해시, 완주하지 못한 선수"
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
![0](https://user-images.githubusercontent.com/31606119/77504260-39670980-6ea3-11ea-8ce8-d835e24d93a7.png)
![1](https://user-images.githubusercontent.com/31606119/77504263-3b30cd00-6ea3-11ea-865e-42e9a718911d.png)
![2](https://user-images.githubusercontent.com/31606119/77504265-3bc96380-6ea3-11ea-8e6c-5d350be92d68.png)

## 내 풀이
```python
def solution(participant, completion):
    participant.sort()
    completion.sort()
    for p, c in zip(participant, completion):
        if p != c:
            return p
    return participant[-1]
```
해시를 안 썼다. 잘 모른다. 불만족스럽다.

## 가장 추천수가 높은 풀이
```python
import collections


def solution(participant, completion):
    answer = collections.Counter(participant) - collections.Counter(completion)
    return list(answer.keys())[0]
```
```collections``` 모듈을 쓴 풀이. 가장 괜찮았다.

## 가장 좋았던 풀이 두 개
* 해시값을 가감하는 아이디어가 신박.  
```collections.Counter()``` 를 알고 나니 골조가 비슷한 느낌이다.

```python
def solution(participant, completion):
    answer = ''
    temp = 0
    dic = {}
    for part in participant:
        dic[hash(part)] = part
        temp += int(hash(part))
    for com in completion:
        temp -= hash(com)
    answer = dic[temp]

    return answer
```

* 어떻게 하면 해시를 써서 풀 수 있을까 고민할 때 들었던 생각과 가장 근접.  
```collections.Counter()``` 를 풀어쓴 느낌이다.

```python
def solution(participant, completion):
    dict_part = {}
    for person in participant:
        if person in dict_part.keys():
            dict_part[person] += 1
        else:
            dict_part[person] = 1
    for indiv in completion:
        dict_part[indiv] -= 1
    answer = [x for x,y in dict_part.items() if y!=0]
    return ''.join(answer)
```

---

## collections.Counter()
```python
import collections
 

count = Counter()                           # a new, empty counter
count = Counter('gallahad')                 # a new counter from an iterable
count = Counter({'red': 4, 'blue': 2})      # a new counter from a mapping
count = Counter(cats=4, dogs=8)             # a new counter from keyword args

count = Counter(['eggs', 'ham'])
c['bacon']                              # 엘리먼트가 없으면 0 리턴
# 0

a_list = ['a', 'b', 'c', 'c', 'a', 'a']
print(collections.Counter(a_list))
# Counter({'a': 3, 'c': 2, 'b': 1})
 
b_list = ['a', 'd', 'c', 'a', 'b']
print(collections.Counter(b_list))
# Counter({'a': 2, 'd': 1, 'b': 1, 'c': 1})
 
result = collections.Counter(a_list) + collections.Counter(b_list)
print(result)
# Counter({'a': 5, 'c': 3, 'b': 2, 'd': 1})

# pandas의 head()와 같다. 상위 n개의 key, value쌍 return
print(result.most_common(3))
# [('a', 5), ('c', 3), ('b', 2)]
```
