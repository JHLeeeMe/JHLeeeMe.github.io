---
date: 2020-03-31 09:15:09
layout: post
title: "AWS Access Key 생성하기"
subtitle:
description:
image:
optimized_image:
category: ETC
tags:
  - ETC
  - AWS
author: JHLeeeMe
paginate: false
---

데이터가 AWS S3에 있고,  
S3에 접근하기 위해선 본인의 AWS계정의 Acess Key를 설정 해야한다.  
AWS 계정이 있다고 가정하고 진행

## 1. IAM 서비스
-- 계정 로그인을 하고 서비스 찾기에서 ```IAM``` 검색 
<a href="https://user-images.githubusercontent.com/31606119/78011497-9caedb00-737e-11ea-9ad7-85efd4301597.png">
![aws_console](https://user-images.githubusercontent.com/31606119/78011497-9caedb00-737e-11ea-9ad7-85efd4301597.png)
</a>

## 2. Group 생성
-- 좌측 메뉴바 에서 그룹 클릭
![IAM_Group](https://user-images.githubusercontent.com/31606119/78013107-f1ebec00-7380-11ea-8de1-edc1f2822cab.png)

-- ```test```라는 이름으로 새 그룹을 생성해보겠다.  
-- ```단계 2```에서 ```S3FullAccess```와 ```IAMFullAccess```정책을 연결하자
![0](https://user-images.githubusercontent.com/31606119/78013080-e993b100-7380-11ea-9aff-465951b98a81.png)
![group](https://user-images.githubusercontent.com/31606119/78013331-498a5780-7381-11ea-874b-1d5c3ed211dd.png)

## 3. User 생성
-- 마찬가지로 좌측 메뉴바 에서 사용자 클릭
![2](https://user-images.githubusercontent.com/31606119/78013090-ebf60b00-7380-11ea-8a2b-cef1158018c0.png)

-- ```사용자 이름```은 test-user  
-- ```엑세스 유형```은 프로그래밍 방식 엑세스로 체크하고
![3](https://user-images.githubusercontent.com/31606119/78013091-ec8ea180-7380-11ea-9395-008c3b084c14.png)

-- 아까 만들었던 ```test Group```을 선택한다.
![4](https://user-images.githubusercontent.com/31606119/78013093-ed273800-7380-11ea-9abf-b987bb992b26.png)

-- ```엑세스 키 ID```와 ```엑세스 키 PASSWORD```가 생성됐다.  
-- 키 password는 다시 확인할 수 없으니 따로 잘 보관한다.
![5](https://user-images.githubusercontent.com/31606119/78013094-edbfce80-7380-11ea-8488-b0d6378f7d38.png)

-- ```test Group```에 속한 ```test-user```를 만들었다.
![6](https://user-images.githubusercontent.com/31606119/78013096-ee586500-7380-11ea-9c01-0d6fb5a95a79.png)

-- 참고) 초기 사용자 생성에서 만들어진 ```엑세스 키 PASSWORD```를 잊어먹었다면  
해당 유저의 ```보안 자격 증명``` 탭에서 ```엑세스 키 만들기``` 버튼을 통해 새로 만들어야 함
![7](https://user-images.githubusercontent.com/31606119/78013099-eef0fb80-7380-11ea-9f92-88ef1887ddd5.png)
