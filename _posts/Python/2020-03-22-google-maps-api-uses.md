---
date: 2020-03-22 12:44:55
layout: post
title: "Google Maps API를 활용한 주소데이터 스크래핑"
subtitle:
description:
image: https://user-images.githubusercontent.com/31606119/77249743-b4070d80-6c86-11ea-940c-87ab16a36e55.png
optimized_image: https://user-images.githubusercontent.com/31606119/77249743-b4070d80-6c86-11ea-940c-87ab16a36e55.png
category: Python
tags:
  - Python
  - Google
  - API
author: JHLeeeMe
paginate: false
---

## 지도 정보를 얻을 수 있는 Google Maps
위치에 대한 검색 결과 중 주소와 위도, 경도 정보를 제공하는 API  

---

## API 설정 순서
1. Google Maps API 주소: [https://cloud.google.com/maps-platform/](https://cloud.google.com/maps-platform/)
![googleMapsAPI_1](https://user-images.githubusercontent.com/31606119/77249743-b4070d80-6c86-11ea-940c-87ab16a36e55.png)

2. 시작하기 버튼을 누르고 지도 체크
![googleMapsAPI_2](https://user-images.githubusercontent.com/31606119/77249745-b5d0d100-6c86-11ea-860d-35925f2f1d21.png)
(장소를 체크하면 geoCodingAPI가 들어있어서 따로 추가를 안해도 됨)
![googleMapsAPI_6](https://user-images.githubusercontent.com/31606119/77249749-b79a9480-6c86-11ea-9e78-a7a3c584f45f.png)

3. 프로젝트 이름을 정하고 Next
![googleMapsAPI_3](https://user-images.githubusercontent.com/31606119/77249746-b6696780-6c86-11ea-9609-5fb0848ff73e.png)

4. API key는 따로 저장해두자.
![googleMapsAPI_4](https://user-images.githubusercontent.com/31606119/77249747-b701fe00-6c86-11ea-8609-e1b84b3b6a0c.png)

5. geocodingAPI를 라이브러리에서 추가
![googleMapsAPI_5](https://user-images.githubusercontent.com/31606119/77249748-b79a9480-6c86-11ea-83c9-d909fb3d9367.png)

---

## Google Maps API 사용해보자.
먼저 터미널에서 ```$ pip install googlemaps```로 설치하고 아래 코드를 실행해보자.  

```python
import googlemaps


gmaps_key = '***************'  # API설정할 때 얻은 key
gmaps = googlemaps.Client(key=gmaps_key)

gmaps.geocode('중부경찰서', language='ko')

---
Output:

[{'address_components': [{'long_name': '２７',
    'short_name': '２７',
    'types': ['premise']},
   {'long_name': '수표로',
    'short_name': '수표로',
    'types': ['political', 'sublocality', 'sublocality_level_4']},
   {'long_name': '을지로동',
    'short_name': '을지로동',
    'types': ['political', 'sublocality', 'sublocality_level_2']},
   {'long_name': '중구',
    'short_name': '중구',
    'types': ['political', 'sublocality', 'sublocality_level_1']},
   {'long_name': '서울특별시',
    'short_name': '서울특별시',
    'types': ['administrative_area_level_1', 'political']},
   {'long_name': '대한민국',
    'short_name': 'KR',
    'types': ['country', 'political']},
   {'long_name': '100-032',
    'short_name': '100-032',
    'types': ['postal_code']}],
  'formatted_address': '대한민국 서울특별시 중구 을지로동 수표로 27',
  'geometry': {'location': {'lat': 37.5636465, 'lng': 126.9895796},
   'location_type': 'ROOFTOP',
   'viewport': {'northeast': {'lat': 37.56499548029149,
     'lng': 126.9909285802915},
    'southwest': {'lat': 37.56229751970849, 'lng': 126.9882306197085}}},
  'place_id': 'ChIJc-9q5uSifDURLhQmr5wkXmc',
  'plus_code': {'compound_code': 'HX7Q+FR 대한민국 서울특별시',
   'global_code': '8Q98HX7Q+FR'},
  'types': ['establishment', 'point_of_interest', 'police']},
 {'address_components': [{'long_name': '７２−１',
    'short_name': '７２−１',
    'types': ['premise']},
   {'long_name': '을지로5가',
    'short_name': '을지로5가',
    'types': ['political', 'sublocality', 'sublocality_level_2']},
   {'long_name': '중구',
    'short_name': '중구',
    'types': ['political', 'sublocality', 'sublocality_level_1']},
   {'long_name': '서울특별시',
    'short_name': '서울특별시',
    'types': ['administrative_area_level_1', 'political']},
   {'long_name': '대한민국',
    'short_name': 'KR',
    'types': ['country', 'political']},
   {'long_name': '100-195',
    'short_name': '100-195',
    'types': ['postal_code']}],
  'formatted_address': '대한민국 서울특별시 중구 을지로5가 72-1',
  'geometry': {'location': {'lat': 37.5664519, 'lng': 127.0039115},
   'location_type': 'ROOFTOP',
   'viewport': {'northeast': {'lat': 37.56780088029149,
     'lng': 127.0052604802915},
    'southwest': {'lat': 37.5651029197085, 'lng': 127.0025625197085}}},
  'place_id': 'ChIJM_LtdCKjfDURUpaP5-pdZSQ',
  'plus_code': {'compound_code': 'H283+HH 대한민국 서울특별시',
   'global_code': '8Q99H283+HH'},
  'types': ['establishment', 'point_of_interest', 'police']}]
```

가지고 있는 데이터를 사용하여 주소데이터를 긁어보기로 했다.
```python
import pandas as pd


# Pandas로 data(csv)를 읽고
crime_anal_police = pd.read_csv('./data/02. crime_in_Seoul.csv', 
				thousands=',',
				index_col=0,
				encoding='euc-kr')
crime_anal_police.head()
```
![googleMapsAPI_7](https://user-images.githubusercontent.com/31606119/77250339-b66b6680-6c8a-11ea-8599-cd7ec96c0320.png)

'중부서'라고 검색하면 안나와서 뒤에 경찰서를 붙였다.  
```e.g. 중부서 -> 중부경찰서```
```python
station_name = []

for name in crime_anal_police.index:
    station_name.append(name[:-1] + '경찰서')

station_name 

---
Output:

['중부경찰서',
 '종로경찰서',
 '남대문경찰서',
 '서대문경찰서',
 '혜화경찰서',
 '용산경찰서',
 '성북경찰서',
 '동대문경찰서',
 '마포경찰서',
 '영등포경찰서',
 '성동경찰서',
 '동작경찰서',
 '광진경찰서',
 '서부경찰서',
 '강북경찰서',
 '금천경찰서',
 '중랑경찰서',
 '강남경찰서',
 '관악경찰서',
 '강서경찰서',
 '강동경찰서',
 '종암경찰서',
 '구로경찰서',
 '서초경찰서',
 '양천경찰서',
 '송파경찰서',
 '노원경찰서',
 '방배경찰서',
 '은평경찰서',
 '도봉경찰서',
 '수서경찰서']
```

가공된 데이터```(station_name)```로 주소와 위도, 경도를 스크래핑
```python
station_address = []
station_lat = []
station_lng = []

for name in station_name:
    tmp = gmaps.geocode(name, language='ko')
    station_address.append(tmp[0].get('formatted_address'))
    
    tmp_loc = tmp[0].get('geometry')
    station_lat.append(tmp_loc['location']['lat'])
    station_lng.append(tmp_loc['location']['lng'])


for address, lat, lng in zip(station_address, station_lat, station_lng):
    print('주소: ' + address)
    print('위도: ' + str(lat) + ' 경도: ' + str(lng))
    print('---------------------------------------')

---
Output:

주소: 대한민국 서울특별시 중구 을지로동 수표로 27
위도: 37.5636465 경도: 126.9895796
---------------------------------------
주소: 대한민국 서울특별시 종로구 종로1.2.3.4가동 율곡로 46
위도: 37.5755578 경도: 126.9848674
---------------------------------------
주소: 대한민국 서울특별시 중구 회현동 한강대로 410
위도: 37.5547584 경도: 126.9734981
---------------------------------------
주소: 대한민국 서울특별시 서대문구 충현동 통일로 113
위도: 37.5647848 경도: 126.9667762
---------------------------------------
주소: 대한민국 서울특별시 종로구 인의동 창경궁로 112-16
위도: 37.5718529 경도: 126.9989143
---------------------------------------
주소: 대한민국 서울특별시 용산구 원효로1가 원효로89길 24
위도: 37.5411211 경도: 126.9676935
---------------------------------------
주소: 대한민국 서울특별시 성북구 삼선동5가 301
위도: 37.5897482 경도: 127.0161353
---------------------------------------
주소: 대한민국 서울특별시 동대문구 청량리동 약령시로21길 29
위도: 37.58506149999999 경도: 127.0457679
---------------------------------------
주소: 대한민국 서울특별시 마포구 아현동 마포대로 183
위도: 37.550814 경도: 126.954028
---------------------------------------
주소: 대한민국 서울특별시 영등포구 영등포동 영등포로46길 14
위도: 37.5179839 경도: 126.9075056
---------------------------------------
주소: 대한민국 서울특별시 성동구 행당동 왕십리광장로 9
위도: 37.5617309 경도: 127.0363806
---------------------------------------
주소: 대한민국 서울특별시 동작구 노량진1동 노량진로 148
위도: 37.5130866 경도: 126.9428498
---------------------------------------
주소: 대한민국 서울특별시 광진구 구의동 자양로 167
위도: 37.542873 경도: 127.083821
---------------------------------------
주소: 대한민국 서울특별시 은평구 대조동 통일로 757
위도: 37.6128611 경도: 126.9274951
---------------------------------------
주소: 대한민국 서울특별시 강북구 번1동 오패산로 406
위도: 37.63730390000001 경도: 127.0273399
---------------------------------------
주소: 대한민국 서울특별시 금천구 시흥동 190
위도: 37.4568722 경도: 126.8970429
---------------------------------------
주소: 대한민국 서울특별시 중랑구 신내1동 신내역로3길 40-10
위도: 37.618692 경도: 127.1047136
---------------------------------------
주소: 대한민국 서울특별시 강남구 대치동 998
위도: 37.5094352 경도: 127.0669578
---------------------------------------
주소: 대한민국 서울특별시 관악구 봉천동
위도: 37.4743789 경도: 126.9509748
---------------------------------------
주소: 대한민국 서울특별시 양천구 신월동 화곡로 73
위도: 37.5397827 경도: 126.8299968
---------------------------------------
주소: 대한민국 서울특별시 강동구 성내1동 성내로 57
위도: 37.528511 경도: 127.1268224
---------------------------------------
주소: 대한민국 서울특별시 성북구 종암동 종암로 135
위도: 37.6020592 경도: 127.0321577
---------------------------------------
주소: 대한민국 서울특별시 구로구 구로동 가마산로 235
위도: 37.494931 경도: 126.886731
---------------------------------------
주소: 대한민국 서울특별시 서초구 서초3동 반포대로 179
위도: 37.4956054 경도: 127.0052504
---------------------------------------
주소: 대한민국 서울특별시 양천구 신정6동 목동동로 99
위도: 37.5165667 경도: 126.8656763
---------------------------------------
주소: 대한민국 서울특별시 송파구 가락본동 9
위도: 37.5019065 경도: 127.1271513
---------------------------------------
주소: 대한민국 서울특별시 노원구 하계동 노원로 283
위도: 37.6425238 경도: 127.0717076
---------------------------------------
주소: 대한민국 서울특별시 서초구 방배본동 동작대로 204
위도: 37.4945959 경도: 126.9831279
---------------------------------------
주소: 대한민국 서울특별시 은평구 불광동 연서로 365
위도: 37.6283597 경도: 126.9287226
---------------------------------------
주소: 대한민국 서울특별시 도봉구 창4동 노해로 403
위도: 37.6533589 경도: 127.052682
---------------------------------------
주소: 대한민국 서울특별시 강남구 개포동 개포로 617
위도: 37.49349 경도: 127.0772119
---------------------------------------
```
