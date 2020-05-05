---
date: 2020-03-28 07:31:13
layout: post
title: "System-Monitoring-Dashboard 만들기 (fluent-bit, influxDB, grafana)"
subtitle:
description:
image: https://user-images.githubusercontent.com/31606119/77818265-6cfc9a80-7114-11ea-91b7-e9fcb0e0ab98.png
optimized_image: https://user-images.githubusercontent.com/31606119/77818265-6cfc9a80-7114-11ea-91b7-e9fcb0e0ab98.png
category: ETC
tags:
  - ETC  
  - Grafana
  - InfluxDB
author: JHLeeeMe
paginate: false
---

# 시나리오
1. fluent-bit로 cpu, disk, network, memory 정보를 1초마다 읽어 들인다.
2. influxDB에 적재한다.
3. grafana로 influxDB의 데이터를 시각화 한다.

---

## 1. fluent-bit로 system metrics 추출
-- fluent-bit 설치는 doc 참조  
doc v1.3: [https://docs.fluentbit.io/manual](https://docs.fluentbit.io/manual)

-- ```/etc/td-agent-bit/td-agent-bit.conf```
```bash
[SERVICE]                                                                
    # Flush                                                              
    # =====                                                              
    # Set an interval of seconds before to flush records to a destination
    Flush        5                                                           
                                                                             
    # Daemon                                                                 
    # ======                                                                 
    # Instruct Fluent Bit to run in foreground or background mode.           
    Daemon       Off                                                         
                                                                             
    # Log_Level                                                              
    # =========                                                              
    # Set the verbosity level of the service, values can be:                 
    #                                                                        
    # - error                                                                
    # - warning                                                              
    # - info                                                                 
    # - debug                                                                
    # - trace                                                                
    #                                                                        
    # By default 'info' is set, that means it includes 'error' and 'warning'.
    Log_Level    info                                                        
                                                                             
    # Parsers_File                                                           
    # ============                                                           
    # Specify an optional 'Parsers' configuration file                       
    Parsers_File parsers.conf                                                
    Plugins_File plugins.conf                                                
                                                                             
    # HTTP Server                                                            
    # ===========                                                            
    # Enable/Disable the built-in HTTP Server for metrics                    
    HTTP_Server  Off                                                         
    HTTP_Listen  0.0.0.0                                                     
    HTTP_Port    2020                                                        
                                                                             
#############################################################################
                                                                             
[INPUT]                                                                      
    Name cpu                                                                 
    Tag  system.cpu                                                          
                                                                             
    # Interval Sec                                                           
    # ====                                                                   
    # Read interval (sec) Default: 1                                         
    Interval_Sec 1                                                           
    #Interval_NSec 0                                                         
    #PID True                       
    
[INPUT]                                                                      
    Name disk                                                                
    Tag  system.disk                                                         
                                                                             
    # Dev_Name                                                               
    # ====                                                                   
    # To limit the target disk/partition.                                    
    # If not set, the plugin gathers from all of disks and partitions.       
    # Dev_Name sda                                                           
                                                                             
    # Total Interval                                                         
    #     = Interval Sec + ( Interval Nsec / 1000 / 1000 / 1000 )            
    #                                                                        
    # Interval Sec                                                           
    # ====                                                                   
    # Read interval (sec) Default: 1                                         
    Interval_Sec 1                                                           
                                                                             
    # Interval NSec                                                          
    # ====                                                                   
    # Read interval (nsec) Default: 0                                        
    Interval_NSec 0                                                          
                                                                             
[INPUT]                                                                      
    Name mem                                                                 
    Tag  system.mem   

#############################################################################

[OUTPUT]
    Name influxdb
    Match system.*
    Host 127.0.0.1
    Port 8086  # influxdb default port
    Database system_metrics_db
```

## 2. influxDB 설치 및 database 생성
-- 설치는 doc 참조  
doc v1.7: [https://docs.influxdata.com/influxdb/v1.7/](https://docs.influxdata.com/influxdb/v1.7/)

-- system_metrics_db 데이터베이스 생성
```bash
#!/usr/bin/env bash

#influxdb start
$ sudo service influxdb start

# influxdb admin으로 접속
$ influx
  > show databases
  > create database system_metrics_db
  > use system_metrics_db
```

## 3. grafana로 dashboard 생성
-- 설치는 doc 참조
주소: [https://grafana.com/docs/grafana/latest/installation/debian/](https://grafana.com/docs/grafana/latest/installation/debian/)

-- 먼저 fluent-bit를 실행해서 influxdb에 system-metrics를 적재하자.  
```$ sudo service td-agent-bit start```

-- grafana 실행  
```$ sudo service grafana-server start```

-- grafana 접속
```ruby
웹 브라우저를 켜고 localhost:3000에 접속해보자. (default-port: 3000)
user: admin
password: admin
```
<a href="https://user-images.githubusercontent.com/31606119/77818257-679f5000-7114-11ea-89ff-97a95b2bba7a.png">
![0](https://user-images.githubusercontent.com/31606119/77818257-679f5000-7114-11ea-89ff-97a95b2bba7a.png)
</a>

-- Create a data source (influxDB)
<a href="https://user-images.githubusercontent.com/31606119/77818258-69691380-7114-11ea-9b25-09e63e34db8f.png">
![1](https://user-images.githubusercontent.com/31606119/77818258-69691380-7114-11ea-9b25-09e63e34db8f.png)
</a>

-- ```InfluxDB default-PORT```: 8086  
-- ```db```: system_metrics_db  
-- ```user/password```: admin/admin
<a href="https://user-images.githubusercontent.com/31606119/77818259-6a01aa00-7114-11ea-873b-83c2c3da7051.png">
![2](https://user-images.githubusercontent.com/31606119/77818259-6a01aa00-7114-11ea-873b-83c2c3da7051.png)
</a>

-- New dashboard  
-- 방금 만든 InfluxDBTest data source 선택
<a href="https://user-images.githubusercontent.com/31606119/77818260-6a9a4080-7114-11ea-8087-e0f19da19c34.png">
![3](https://user-images.githubusercontent.com/31606119/77818260-6a9a4080-7114-11ea-8087-e0f19da19c34.png)
</a>

-- SQL문으로 편집하기위해 연필 모양 아이콘 클릭
<a href="https://user-images.githubusercontent.com/31606119/77818261-6b32d700-7114-11ea-9bd4-20f501ed9954.png">
![4](https://user-images.githubusercontent.com/31606119/77818261-6b32d700-7114-11ea-9bd4-20f501ed9954.png)
</a>
<a href="https://user-images.githubusercontent.com/31606119/77818262-6bcb6d80-7114-11ea-8581-1578fb68f58e.png">
![5](https://user-images.githubusercontent.com/31606119/77818262-6bcb6d80-7114-11ea-8581-1578fb68f58e.png)
</a>

-- ```field key``` 는 influxDB에서 확인하면 된다.
```bash
$ influx
  > show databases
  > use system_metrics_db
  > show field keys
```
<a href="https://user-images.githubusercontent.com/31606119/77818263-6c640400-7114-11ea-9218-dda69a27d4ba.png">
![6](https://user-images.githubusercontent.com/31606119/77818263-6c640400-7114-11ea-9218-dda69a27d4ba.png)
</a>
-- fluent-bit에서 설정한 ```tag name``` 으로 ```테이블(?) 네임```이 결정된다.  
-- 기존에 있던걸 찍어서 위에 설정한 것과 이름이 다를 것이다.

-- ```Visualization Tab```에서 여러 가지 설정을 만질 수 있음.
<a href="https://user-images.githubusercontent.com/31606119/77818264-6cfc9a80-7114-11ea-83f9-e99b437f547e.png">
![7](https://user-images.githubusercontent.com/31606119/77818264-6cfc9a80-7114-11ea-83f9-e99b437f547e.png)
</a>

---

## 결과 화면
<a href="https://user-images.githubusercontent.com/31606119/77818265-6cfc9a80-7114-11ea-91b7-e9fcb0e0ab98.png">
![8](https://user-images.githubusercontent.com/31606119/77818265-6cfc9a80-7114-11ea-91b7-e9fcb0e0ab98.png)
</a>
Google Compute Engine에서 적용하는데  
free tier라 influxdb는 메모리 부족으로 켜지지도 않고,  
컴퓨터 멈추고 그래서 사양 업그레이드를 했다.  
Host PC에서 접속하기 위해 방화벽에서 3000번 포트를 뚫어주었다.  
disk는 fluent-bit 기본값으로 read, write 두 개로 돼있나 보다.  
생각했던 건 사용 중인 용량하고 전체 용량이었는데...  
그게 중요한 건 아니라 따로 알아보진 않았다.

---

# 추가) Grafana 알림 설정하기
내 글: [Grafana에서 Alert 설정하기 (Email 알림)](https://jhleeeme.github.io/alerting-grafana/)
