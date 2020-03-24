---
date: 2020-03-22 05:28:33
layout: post
title: "Oh-My-Zsh로 테마 적용하기"
subtitle:
description:
image: https://user-images.githubusercontent.com/31606119/77243334-30c8c600-6c4c-11ea-9004-5168d544b939.png
optimized_image: https://user-images.githubusercontent.com/31606119/77243334-30c8c600-6c4c-11ea-9004-5168d544b939.png
category: ETC
tags:
  - ETC
  - ZSH
  - Oh-My-Zsh!
author: JHLeeeMe
paginate: false
---

기존에 쓰던 테마가 지겨워져서 살펴보던 중  
만들기 쉬워 보여서 만들어봤다.

그전에 설치가 안돼 있다면,  
[Ubuntu18.04에서 ZSH 및 Oh-My-Zsh로 터미널 꾸미기](https://jhleeeme.github.io/ubuntu18.04%EC%97%90%EC%84%9C-zsh-%EB%B0%8F-oh-my-zsh%EB%A1%9C-%ED%84%B0%EB%AF%B8%EB%84%90-%EA%BE%B8%EB%AF%B8%EA%B8%B0/) 참고

---

ZSH 테마는 ```~/.oh-my-zsh/themes/``` OR ```~/.oh-my-zsh/custom/themes/```에 두면 된다.  
***{theme-name}.zsh-theme***이라는 파일로 존재한다.  
```~/.oh-my-zsh/oh-my-zsh.sh```을 보면 둘 중 어디에 있어도 상관없다.
![oh-my-zsh](https://user-images.githubusercontent.com/31606119/77243175-8308e780-6c4a-11ea-95b6-6439c5663fe2.png)

```{theme-name}.zsh-theme```파일을 위에 명시해둔 디렉터리 중 한곳에 넣고
![add_theme_in_custom_directory](https://user-images.githubusercontent.com/31606119/77243198-ded37080-6c4a-11ea-8d16-9903a4604455.png)

```~/.zshrc```에서 ***ZSH_THEME***을 수정하고
![zshrc](https://user-images.githubusercontent.com/31606119/77243232-19d5a400-6c4b-11ea-84ef-a17d7523c3d1.png)

```$ source ~/.zshrc```로 적용하면 끝

---

# JHLeeeMe-Zsh-Theme
![my_theme](https://user-images.githubusercontent.com/31606119/77243334-30c8c600-6c4c-11ea-9004-5168d544b939.png)

설치법은 본인의 Github에도 작성해두었다.  
Github: [JHLeeeMe/JHLeeeMe-Zsh-Theme](https://github.com/JHLeeeMe/JHLeeeMe-Zsh-Theme)  

---

## 다양한 ZSH Theme
Github: [ohmyzsh/ohmyzsh/wiki/Themes](https://github.com/ohmyzsh/ohmyzsh/wiki/Themes)  
Github: [ohmyzsh/ohmyzsh/wiki/External-themes](https://github.com/ohmyzsh/ohmyzsh/wiki/External-themes)  
Github: [unixorn/awesome-zsh-plugins](https://github.com/unixorn/awesome-zsh-plugins)
