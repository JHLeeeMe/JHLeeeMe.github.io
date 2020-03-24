---
date: 2020-03-24 06:16:35
layout: post
title: "vscode 삭제하기"
subtitle: "build failed, do you want to continue? 에러 메세지"
description:
image: https://user-images.githubusercontent.com/31606119/77394614-2ab91d80-6de3-11ea-9ec1-1d3446c6b04f.png
optimized_image: https://user-images.githubusercontent.com/31606119/77394614-2ab91d80-6de3-11ea-9ec1-1d3446c6b04f.png
category: ETC
tags:
  - ETC
  - error
  - vscode
  - IDE
author: JHLeeeMe
paginate: false
---

vscode에서 java를 다루고 있는데  
잘 되다가 갑자기 클래스 패스가 잘못됐다던지 하는...  
아래와 같은 에러 메세지를 보았다.
![0](https://user-images.githubusercontent.com/31606119/77394614-2ab91d80-6de3-11ea-9ec1-1d3446c6b04f.png)

또는 classpath error가 뜨기도 햇다.  
일단 vscode를 껐다가 켜봐도 안되고...  
구글링을 통해 해결방법을 찾을려고 했는데 잘 안된다..(그냥 뭔가 꼬인 느낌)  
그래서 결국 vscode 설정 파일들이 모여있는 곳의 워크스페이스 디렉터리를 삭제했다.  
아래 주소에서 써있는데로...  
Github 주소: [VSCode Troubleshooting](https://github.com/redhat-developer/vscode-java/wiki/Troubleshooting)

---

만약 이래도 해결이 안된다면 vscode를 완전히 삭제하고 다시 깔아보자...  
- ```~/.config/Code/``` -> 설정들 관리되는 디렉터리
- ```~/.vscode/``` -> 설치했던 플러그인들 관리되는 디렉터리 

위 두 디렉터리와 vscode를 삭제하고 재설치 해보자.
