---
date: 2020-03-21 07:06:18
layout: post
title: "Ubuntu18.04에서 xmodmap을 이용한 key remap"
subtitle:
description:
image: https://user-images.githubusercontent.com/31606119/77221853-0108b800-6b91-11ea-81cc-e69b06666577.jpg
optimized_image: https://user-images.githubusercontent.com/31606119/77221853-0108b800-6b91-11ea-81cc-e69b06666577.jpg
category: ETC
tags:
  - ETC
  - xmodmap
author: JHLeeeMe
paginate: false
---

msi gl62m 7rc 사용 중  
키보드의 insert, delete, home, end, pageUp, pageDown을 엄청나게 사용하는데 

게이밍 노트북이라 그런지 키보드 레이아웃이 더러움.  
그래서 numpad의 7, 4, 8, 5, 9, 6을  
insert, delete, home, end, pageUp, pageDown 으로 리맵핑 할 계획

---

일단 잘못될 수 있으니 수정 전 상태를 ```keymap_origianl```이라는 이름으로 저장  
```$ xmodmap -pke > ~/xmodmap_original```

### .Xmodmap 파일 생성 (수정할 파일)
```$ xmodmap -pke > ~/.Xmodmap```

### xev를 실행시켜 키 code를 알아낸다.
```$ xev```

내 노트북의 key code  
insert = 118, del = 119, home = 110, end = 115, pageUp = 112, pageDown = 117  
numpad_7 = 79,  numpad_4 = 80,  numpad_8 = 81,  numpad_5 = 83,  numpad_9 = 84, numpad_6 = 85  

### .Xmodmap 을 열고 내용 수정
```$ vi ~/.Xmodmap```

key code 118의 내용을 key code 79에 옮겨 적는다.  
ex) ```keycode  79 = KP_Home KP_7 KP_Home KP_7```로 돼있는걸  
```keycode  79 = Insert NoSymbol Insert```로...  
(keycode 118 = Insert NoSymbol Insert)

이런 식으로 위에서 알아낸 넘패드 key code 내용들을 죄다 바꿔버린다.

### 바뀐 걸 적용하기 (조금 오래 걸림)
```$ xmodmap ~/.Xmodmap```

잘된다. 근데 numpad 활성화해도 숫자를 못쓰는 중이다.. 쓰지도 않지만

---

잘 되는 줄 알았는데 reboot 하니깐 자동실행이 안된다.  
그래서 실행 권한을 줘봤다.  
```$ sudo chmod 755 ~/.Xmodmap```

그래도 안된다...  
시작 프로그램에다가 추가시켜버림  
명령 : ```xmodmap /home/{username}/.Xmodmap```
![xmodmap_0](https://user-images.githubusercontent.com/31606119/77222195-6e6a1800-6b94-11ea-8e3c-64ee28393f66.png)
![xmodmap_1](https://user-images.githubusercontent.com/31606119/77222196-7033db80-6b94-11ea-9967-53eb7fd86bba.png)

잘 된다.  
로그인할 때 2초 정도 느려진 느낌인데 아닐 수도  
맞다면 터미널에서 실행했을 때는 10초 정도 걸렸었는데.. 감당가능  

---

잘 되는 것 같더니 또 안된다.  
.Xmodmap의 읽기 권한을 줘봤다.  
그냥 모든 권한을 다 줌  
```$ sudo chmod 777 ~/.xmodmap```
 
잘 된다.  
xmodmap 명령으로 ```~/.Xmodmap```을 읽어야 되기 때문 으로 생각된다.  
그러면 애초에 실행권한이 아니라 읽기권한으로 줬으면 따로 시작 프로그램에 등록을 안해도 됐을 것 같다.  
잘 되는데 또 안되면 수정하겠다.
