---
date: 2020-03-20 06:25:38
layout: post
title: "Ubuntu 18.04에서 pyenv로 python설치 시 openssl 에러 해결방법"
subtitle: 
description: "Ubuntu 18.04에서 pyenv로 python설치 시 openssl 에러 해결방법"
image: https://user-images.githubusercontent.com/31606119/77141408-2a0d4800-6ac0-11ea-82c2-daa0a1e2e8ea.png
optimized_image: https://user-images.githubusercontent.com/31606119/77141408-2a0d4800-6ac0-11ea-82c2-daa0a1e2e8ea.png
category: ETC
tags:
  - ETC
  - error
  - pyenv
author: JHLeeeMe
paginate: false
---

### pyenv로 해당 버전 install
```$ pyenv install {version}```

### 그런데 에러가 났다.
``` 'The Python ssl extension was not compiled. Missing the OpenSSL lib?' ```

### 필요 패키지들을 설치해도 같은 에러
```bash
#!/usr/bin/env bash

$ sudo aptinstall -y make build-essential libssl-dev zlib1g-dev libbz2-dev \
libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev \
xz-utils tk-dev libffi-dev liblzma-dev python-openssl git
```

### 우분투 18.04 버전에서는 이걸 설치해주라고 함
```$ sudo apt install openssl1.0-dev```

해결됨
