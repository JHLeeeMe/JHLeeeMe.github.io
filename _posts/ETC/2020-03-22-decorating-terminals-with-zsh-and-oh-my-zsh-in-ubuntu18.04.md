---
date: 2020-03-22 02:00:14
layout: post
title: "Ubuntu18.04에서 ZSH 및 Oh-My-Zsh로 터미널 꾸미기"
subtitle:
description:
image: https://user-images.githubusercontent.com/31606119/77241401-ebe56500-6c34-11ea-879b-517d04720ede.png
optimized_image: https://user-images.githubusercontent.com/31606119/77241401-ebe56500-6c34-11ea-879b-517d04720ede.png
category: ETC
tags:
  - ETC
  - ZSH
  - Oh-My-Zsh!
author: JHLeeeMe
paginate: false
---

## ZSH 설치
```$ sudo apt install zsh```

## ZSH 경로 확인
```$ which zsh```

## 기본 shell을 ZSH로 변경
```bash
$ chsh -s /usr/bin/zsh

OR

$ chsh -s `which zsh`
```


이제 터미널을 새로 열면 ZSH메뉴가 나온다.
![zsh_init](https://user-images.githubusercontent.com/31606119/77241107-2cdb7a80-6c31-11ea-8565-3fc37ed48130.png)
직접 설정은 1번, 귀찮으면 2번  
여기까지가 ```ZSH```설치 

---

## Oh-My-Zsh! 설치 
ZSH의 Theme, Plugin등을 관리하는 프로그램  
```bash
curl:
$ sh -c "(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

OR  

wget:
$ sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```

Oh-My-Zsh! 설치도 끝났고...  
이것을 이용해 테마와 plugin을 적용시켜보자.  

## Theme 설정
먼저 테마는 기본으로 몇개 깔려있다.  
그 중 ```agnoster```테마를 적용시켜보자.  
```.bashrc```에 bash설정을 했던 것처럼 ```.zshrc```에 설정하면 된다.  
```$ sudo vi ~/.zshrc```
![zshrc_theme_agnoster](https://user-images.githubusercontent.com/31606119/77241109-2d741100-6c31-11ea-8797-b54325634b7e.png)

## Plugin 설정
1. zsh-autosuggetions
2. zsh-syntax-highlighting
3. alias-tips  
위의 세가지 플러그인을 설치해보자.  
기본으로 추가돼있는 플러그인이 아니므로 ```git clone```하자.
```bash
$ cd ~/.oh-my-zsh/plugins/
$ git clone https://github.com/zsh-users/zsh-autosuggestions
$ git clone https://github.com/zsh-users/zsh-syntax-highlighting
$ git clone https://github.com/djui/alias-tips.git
```
다시 ```.zshrc```을 열고 아래 처럼 설정하자.
![zshrc_plugins](https://user-images.githubusercontent.com/31606119/77241108-2d741100-6c31-11ea-8618-046c760ca3ea.png)

현재 본인 터미널
![my_terminal_agnoster](https://user-images.githubusercontent.com/31606119/77241104-2baa4d80-6c31-11ea-8529-29667c157a56.png)

아마 폰트가 깨질 것이다.  

폰트를 다운로드 & 인스톨 하고  
```$ git clone https://github.com/powerline/fonts.git```  
```$ fonts/install.sh```  

터미널 편집 탭의 기본 설정에서 글꼴을 바꿔주자.  
우분투 모노 데리버티브 파워라인 레귤러 말고도 안 깨지는 기존 폰트들이 있음.
![terminal_settings](https://user-images.githubusercontent.com/31606119/77241106-2c42e400-6c31-11ea-92f1-162beaaca8da.png)

아, alias-tips는  
예를들면, ```$ git status```라고 쳤을 때
![alias-tips](https://user-images.githubusercontent.com/31606119/77241102-2b11b700-6c31-11ea-8000-b3a4da1df980.png)
이런 식으로 gst만 입력해도 된다고 알려줌.

---

테마가 지겨워져 한번 만들어봤다. 깔끔하게...  

내 글: [Oh-My-Zsh로 테마 적용하기](https://jhleeeme.github.io/oh-my-zsh%EB%A1%9C-%ED%85%8C%EB%A7%88-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0/)  
Github: [본인이 만들어본 Zsh-Theme](https://github.com/JHLeeeMe/JHLeeeMe-Zsh-Theme)
