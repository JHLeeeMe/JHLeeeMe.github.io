---
date: 2020-05-02 16:45:54
layout: post
title: "Grafana에서 Alert 설정하기 (Email 알림)"
subtitle:
description:
image:
optimized_image:
category: ETC
tags: 
  - ETC
  - Grafana
author: JHLeeeMe
paginate: false
---

OS: Ubuntu18.04  
Grafana: v6.7.3

# Grafana Alerting
-- 설정한 조건에 부합할 때 알림메세지를 구글의 smtp서버를 이용해 gmail로 받고자 한다.

## 시나리오
-- 1. 보안 수준이 낮은 앱의 액세스 ON  
-- 2. SSMTP 설치 및 설정  
-- 3. ```grafana.ini``` 수정  
-- 4. Notification channel 생성, 적용

---

## 1. 보안 수준이 낮은 앱의 엑세스 ON
외부에서의 연결을 허용해야 하므로 아래 사진처럼 설정을 ON으로 바꿔준다.
![google_access](https://user-images.githubusercontent.com/31606119/80871513-0442a800-8ce8-11ea-80e8-72bab88dd3dc.jpg)

## 2. SSMTP 설치 및 설정
-- ssmtp에 관한 자세한 내용은 [여기](https://wiki.ubuntu-kr.org/index.php/SSMTP)를 참고  

-- 2-1. 설치: ```$ sudo apt install ssmtp```  
-- 2-2. conf파일 수정 
```bash
$ sudo vi /etc/ssmtp/ssmtp.conf
  # 추가
  root=<user>@gmail.com
  mailhub=smtp.gmail.com:587
  hostname=<hostname>
  FromLineOverride=YES
  AuthUser=<user>@gmail.com
  AuthPass=<password>
  UseTLS=YES
  UseSTARTTLS=YES
```
```AuthPass```부분에 자신의 비밀번호를 적어놓기 싫다면 [여기](https://support.google.com/accounts/answer/185833)를 참고하여 앱 비밀번호를 하나 만들어서 써넣자.

-- 2-3. SSMTP 테스트
```bash
#!/usr/bin/env bash


$ echo 'test' |ssmtp <user>@gmail.com
```
자신의 gmail을 확인해보면 test라는 내용의 메일을 받았을 것.

## 3. grafana.ini 수정
-- 3-1. grafana.ini를 열고  
```$ sudo vi `locate grafana.ini` ```

-- 3-2. 아래 그림과 같이 수정
<a href="https://user-images.githubusercontent.com/31606119/80872189-520cdf80-8ceb-11ea-9af4-38a64c985155.png">
![0](https://user-images.githubusercontent.com/31606119/80872189-520cdf80-8ceb-11ea-9af4-38a64c985155.png)
</a>

-- 3-3. grafana-server 재시작  
```$ sudo service grafana-server restart```

## 4. Notification channel 생성, 적용
-- 4-1. Notification channels 클릭
<a href="https://user-images.githubusercontent.com/31606119/80872459-0e1ada00-8ced-11ea-891e-0da0a9320922.png">
![Notifacation_channels](https://user-images.githubusercontent.com/31606119/80872459-0e1ada00-8ced-11ea-891e-0da0a9320922.png)
</a>

-- 4-2. 아래 그림과 같이 channel을 새로 하나 만든다. 
Addresses 부분은 알림을 받을 이메일을 적어놓는다.
<a href="https://user-images.githubusercontent.com/31606119/80872567-aa44e100-8ced-11ea-8804-d7fa09eda00f.png">
![스크린샷, 2020-05-03 03-14-59](https://user-images.githubusercontent.com/31606119/80872567-aa44e100-8ced-11ea-8804-d7fa09eda00f.png)
</a>

dashboard에서 메모리부분 edit에서 설정한 Notifications를 적용한 그림
<a href="https://user-images.githubusercontent.com/31606119/80873028-c007d580-8cf0-11ea-886e-1f880f072e2e.png">
![grafana_alerting](https://user-images.githubusercontent.com/31606119/80873028-c007d580-8cf0-11ea-886e-1f880f072e2e.png)
</a>
최근 5분동안 사용중인 메모리가 7기가를 넘었을 때 Notification에서 설정한 메일로 알림을 발송하게 했다.

---

# 참고자료
Link: [https://2hyes.tistory.com/76](https://2hyes.tistory.com/76)
