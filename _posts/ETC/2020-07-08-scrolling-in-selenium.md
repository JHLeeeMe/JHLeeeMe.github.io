---
date: 2020-07-08 13:23:35
layout: post
title: "Selenium에서 스크롤링 하기"
subtitle:
description:
image: https://user-images.githubusercontent.com/31606119/86946876-f8f77700-c185-11ea-9ada-64ecb63e99eb.png
optimized_image: https://user-images.githubusercontent.com/31606119/86946192-fd6f6000-c184-11ea-9044-d0380a87263e.png
category: ETC
tags:
  - ETC
  - Selenium
author: JHLeeeMe
paginate: false
---

최근에 토이 프로젝트를 하나 완성시켰다.  
하루에 한번씩 23시에 brunch의 오늘 올라온 글들을 스크래핑 해서  
github의 Issues로 등록해주는 자동화 프로젝트이다.  
[https://github.com/JHLeeeMe/todays-brunch](https://github.com/JHLeeeMe/todays-brunch)  

처음엔 ```requests``` + ```BeautifulSoup```으로 간단하게 할려고 했는데,  
동적 페이지라 원하는 정보를 원하는데로 못긁어서   
```Selenium``` + ```BeautifulSoup```의 조합으로 작업을 완성시켰다.  

brunch의 글들은 스크롤을 내리면 글이 계속 나온다.(무한 스크롤)  
그래서 ```Selenium```으로 스크롤링 하는 방법을 찾아봤고,  
방법이 여러가지 이길래 정리를 하고자 한다.

---

## 방법
  1. ```내장 메서드```를 통한 스크롤링
  2. ```JavaScript``` 실행
  3. ```Keys``` 를 활용한 스크롤링
  4. ```ActionChains``` 으로 일련의 작업들을 한번에 실행 

위 방법들을 통해  
아래 사진에서 보여지는 brunch의 'IT 트렌드' 요소까지 스크롤링 해보자. 
![brunch_it_trend](https://user-images.githubusercontent.com/31606119/86933158-37386a80-c175-11ea-89b4-316a6e9c5d71.png)

---

## 1. 내장 메서드
  - element.```location_once_scrolled_into_view```
  - ```Return``` element의 x, y 좌표

```python
#!/usr/bin/env python3

from selenium import webdriver


driver = webdriver.Chrome('크롬드라이버 경로')
driver.get('https://www.brunch.co.kr/')

element = driver.find_element_by_css_selector(
    '#mArticle > div.keywords > div.keyword_list_wrap > div > a:nth-child(4)'
    )
location = element.location_once_scrolled_into_view

print(location)

--------------------------------------------
Output:

{'y': 1, 'x': 360}
```
위 코드중에 driver.```find_element_by_css_selector()```메서드는  
element를 return 해주는데 파라미터 값을 쉽게 취하는 방법은 아래와 같다.  
크롬이라면 ```Ctrl + Shift + c```를 누르고 해당 요소를 마우스로 크릭하면 바로 잡힘
<a href="https://user-images.githubusercontent.com/31606119/86931040-9c3e9100-c172-11ea-8ff8-e220277a4bf9.png">
![brunch_copy_selector](https://user-images.githubusercontent.com/31606119/86931040-9c3e9100-c172-11ea-8ff8-e220277a4bf9.png)
</a>


## 2. script 실행
  - driver.```execute_script('스크립트', element)``` 메서드를 통한 script문 실행

```python
#!/usr/bin/env python3

from selenium import webdriver


webdriver.Chrome('크롬드라이버 경로')
driver.get('https://www.brunch.co.kr/')

element = driver.find_element_by_css_selector(
    '#mArticle > div.keywords > div.keyword_list_wrap > div > a:nth-child(4)'
    )
driver.execute_script('arguments[0].scrollIntoView(true);', element)
```


## 3. Keys를 활용한 스크롤링
  - 키보드 입력 이벤트를 발생시켜보자

```python
#!/usr/bin/env python3

from selenium import webdriver
from selenium.webdriver.common.keys import Keys


driver = webdriver.Chrome('크롬드라이버 경로')
driver.get('https://www.brunch.co.kr/')

element = driver.find_element_by_tag_name('body')
element.send_keys(Keys.END)
```
브라우저가 활성화된 상태에서 End키를 누른것과 같다.  
```Keys.UP```, ```Keys.DOWN```, ```Keys.PAGE_DOWN```등 여러 키들이 있다.  
자세한 내용은 [여기](https://www.selenium.dev/selenium/docs/api/py/webdriver/selenium.webdriver.common.keys.html?#selenium.webdriver.common.keys)


## 4. ActionChains
  - 키 입력, 요소 클릭 등 일련의 작업들을 체이닝 and 실행
  - 이번엔 'IT 트렌드' 요소까지 스크롤링하고, 클릭까지 해보자.

```python
#!/usr/bin/env python3

from selenium import webdriver
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.common.action_chains import ActionChains


driver = webdriver.Chrome('크롬드라이버 경로')
driver.get('https://www.brunch.co.kr/')

element = driver.find_element_by_css_selector(
    '#mArticle > div.keywords > div.keyword_list_wrap > div > a:nth-child(4)'
    )
actions = ActionChains(driver) \
    .move_to_element(element) \
    .click()

actions.perform()  # actions 실행
```
ActionChains.```move_to_element()``` 메서드는 마우스를 파라미터로 주어진 요소로 이동하는 메서드이며,  
요소는 로딩이 돼있는데 초기 화면에 들어와있지 않다면 스크롤이 조정된다.  
즉, 화면의 가장 아래쪽에 요소가 위치해 있을 것이다.  
```click()``` 메서드는 설명 안해도 뭐...  
여러 메서드들이 있다. 여러 작업들을 할 수 있겠다.  
간단하게 로그인 이라던가. 드래그앤 드롭 등..  
Doc을 살펴보면 좋겠다. [여기](https://www.selenium.dev/selenium/docs/api/py/webdriver/selenium.webdriver.common.action_chains.html#module-selenium.webdriver.common.action_chains)

---

네 가지 방법을 살펴봤다.  
다 비슷한 것 같다.  
element 객체를 취하고, 그것을 이용해 어찌저찌 하면되시겠다...  
특히나 ActionChains가 참 잘빠졌다.  
내 이번 프로젝트엔 쓸 필요가 없었지만, 문서 읽는데 막 설렘.
오늘도 지식이 늘었다.

