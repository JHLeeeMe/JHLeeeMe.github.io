---
date: 2020-03-20 07:45:22
layout: post
title: "Jupyter notebook에서 한글 폰트 설정하기"
subtitle:
description:
image: https://user-images.githubusercontent.com/31606119/77144908-6b0a5a00-6aca-11ea-8f8e-32eadbfba2ac.png
optimized_image: https://user-images.githubusercontent.com/31606119/77144908-6b0a5a00-6aca-11ea-8f8e-32eadbfba2ac.png
category: etc
tags:
  - etc
  - jupyter
  - matplotlib
author: JHLeeeMe
paginate: false
---

## 0. 글꼴 설치 & 확인

apt로 nanum글꼴 설치 및 fc-cache명령으로 폰트 캐시 삭제
```bash
$ sudo apt install fonts-nanum
$ sudo fc-cache -fv
```

Ubuntu의 폰트가 저장돼있는 디렉터리
```bash
$ ls -la /usr/share/fonts

---

합계 24
drwxr-xr-x  6 root 4096  8월  6 04:03 X11/
drwxr-xr-x  2 root 4096  8월  6 04:00 cMap/
drwxr-xr-x  2 root 4096  8월  6 04:00 cmap/
drwxr-xr-x  4 root 4096  8월  6 04:01 opentype/
drwxr-xr-x 49 root 4096 12월  3 15:34 truetype/
drwxr-xr-x  3 root 4096  8월  6 04:01 type1/
```




```bash
$ ls -la /usr/share/fonts/truetype/

---

합계 188
drwxr-xr-x 2 root 4096  8월  6 04:01 Gargi/
drwxr-xr-x 2 root 4096  8월  6 04:01 Gubbi/
drwxr-xr-x 2 root 4096  8월  6 04:01 Nakula/
drwxr-xr-x 2 root 4096  8월  6 04:01 Navilu/
drwxr-xr-x 2 root 4096  8월  6 04:01 Sahadeva/
drwxr-xr-x 2 root 4096  8월  6 04:01 Sarai/
drwxr-xr-x 2 root 4096  8월  6 04:00 abyssinica/
drwxr-xr-x 2 root 4096  8월  6 04:00 dejavu/
drwxr-xr-x 2 root 4096  8월  6 04:00 droid/
drwxr-xr-x 2 root 4096  8월  6 04:01 fonts-beng-extra/
drwxr-xr-x 2 root 4096  8월  6 04:00 fonts-deva-extra/
drwxr-xr-x 2 root 4096  8월  6 04:01 fonts-gujr-extra/
drwxr-xr-x 2 root 4096  8월  6 04:01 fonts-guru-extra/
drwxr-xr-x 2 root 4096  8월  6 04:00 fonts-kalapi/
drwxr-xr-x 2 root 4096  8월  6 04:01 fonts-orya-extra/
drwxr-xr-x 2 root 4096  8월  6 04:01 fonts-telu-extra/
drwxr-xr-x 2 root 4096  8월  6 04:01 freefont/
drwxr-xr-x 2 root 4096  8월  6 04:00 kacst/
drwxr-xr-x 2 root 4096  8월  6 04:00 kacst-one/
drwxr-xr-x 2 root 4096  8월  6 04:01 lao/
drwxr-xr-x 2 root 4096  8월  6 04:00 liberation/
drwxr-xr-x 2 root 4096  8월  6 04:01 liberation2/
drwxr-xr-x 2 root 4096  8월  6 04:01 lohit-assamese/
drwxr-xr-x 2 root 4096  8월  6 04:01 lohit-bengali/
drwxr-xr-x 2 root 4096  8월  6 04:01 lohit-devanagari/
drwxr-xr-x 2 root 4096  8월  6 04:01 lohit-gujarati/
drwxr-xr-x 2 root 4096  8월  6 04:01 lohit-kannada/
drwxr-xr-x 2 root 4096  8월  6 04:01 lohit-malayalam/
drwxr-xr-x 2 root 4096  8월  6 04:01 lohit-oriya/
drwxr-xr-x 2 root 4096  8월  6 04:01 lohit-punjabi/
drwxr-xr-x 2 root 4096  8월  6 04:01 lohit-tamil/
drwxr-xr-x 2 root 4096  8월  6 04:01 lohit-tamil-classical/
drwxr-xr-x 2 root 4096  8월  6 04:01 lohit-telugu/
drwxr-xr-x 2 root 4096  8월  6 04:00 malayalam/
drwxr-xr-x 2 root 4096 12월  3 15:34 nanum/
drwxr-xr-x 2 root 4096  8월  6 04:01 noto/
drwxr-xr-x 2 root 4096 10월 24 21:20 openoffice/
drwxr-xr-x 2 root 4096  8월  6 04:01 padauk/
drwxr-xr-x 2 root 4096  8월  6 04:01 pagul/
drwxr-xr-x 2 root 4096  8월  6 04:01 samyak/
drwxr-xr-x 2 root 4096  8월  6 04:01 samyak-fonts/
drwxr-xr-x 2 root 4096  8월  6 04:01 sinhala/
drwxr-xr-x 2 root 4096  8월  6 04:01 tibetan-machine/
drwxr-xr-x 2 root 4096  8월  6 04:01 tlwg/
drwxr-xr-x 2 root 4096 10월 25 17:32 ttf-bitstream-vera/
drwxr-xr-x 2 root 4096  8월  6 04:01 ttf-khmeros-core/
drwxr-xr-x 2 root 4096  8월  6 04:01 ubuntu/
```

### Matplotlib 폰트 정보 관리

matplotlib는 사용할 font정보를 fontList.json에서 관리함
0. 나눔 글꼴을 matplotlib에 복사하고
1. 자동으로 생성되므로 삭제해주고 최신화 시켜주어야한다.

본인은 pyenv를 통해 파이썬 버전을 관리하고있기 때문에 경로가 다르다.  
**default-path:** /usr/local/lib/[python-version]/dist-packages/matplotlib/mpl-data/fonts/ttf/


```bash
$ sudo cp /usr/share/fonts/truetype/nanum/Nanum* ~/.pyenv/versions/jupyter-3.5.2/lib/python3.5/site-packages/matplotlib/mpl-data/fonts/ttf
$ rm -rf ~/.cache/matplotlib/*
```

삭제해야 할 캐시
![output_9_0](https://user-images.githubusercontent.com/31606119/77145869-0e5c6e80-6acd-11ea-8e2f-fd68086f134c.png)



---
## 1. matplotlib에서 사용 가능한 ttf폰트 목록 확인
jupyter notebook 상에서 진행  

'Nanum'이 포함된 목록만 추출
```python
import matplotlib
import matplotlib.font_manager

[f.name for f in matplotlib.font_manager.fontManager.ttflist if 'Nanum' in f.name]
```

```
Output
---

['NanumSquare',
 'NanumMyeongjo',
 'NanumSquareRound',
 'NanumMyeongjo',
 'NanumBarunGothic',
 'NanumGothic',
 'NanumSquare',
 'NanumBarunGothic',
 'NanumGothic',
 'NanumSquareRound']
```
---
## 2. 전역 글꼴 설정
matplotlib.rcParams를 통해 matplotlib의 다양한 설정값을 읽거나 지정할 수 있다.


```python
import platform
from matplotlib import font_manager, rc
import matplotlib.pyplot as plt

# 한글 사용시 마이너스 폰트가 깨지는 문제가 발생할 수 있으므로 설정변경
plt.rcParams['axes.unicode_minus'] = False

if platform.system() == 'Windows':
    path = "c:/Windows/Fonts/malgun.ttf"
    font_name = font_manager.FontProperties(fname=path).get_name()
    rc('font', family=font_name)
elif platform.system() == 'Darwin':
    rc('font', family='AppleGothic')
elif platform.system() == 'Linux':
    rc('font', family='NanumBarunGothic')
else:
    print('Unknown system... sorry~~~~~~')
```

---
참고자료:
0. [matplotlib-hangul-for-ubuntu-linux](https://financedata.github.io/posts/matplotlib-hangul-for-ubuntu-linux.html)
1. [taewan.kim blog](http://taewan.kim/post/matplotlib_hangul/)
