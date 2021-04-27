---
layout: post
title: "[Python] pip Syntax Error 해결 방법"
description: >
    Linux 환경에서 pip 명령어 사용 시 SyntaxError가 발생했을 때 해결하는 방법.
---

Linux 환경에서 pip 명령어를 사용했을 때 아주 가끔 아래와 같이 SyntaxError: invalid syntax 에러가 발생하는 경우가 있다. 찾아보니 ```$ pip install --upgrade pip``` 명령어를 통해 pip 버전을 업그레이드했을 때 python 2.7 버전으로 변경이 되어서 그런 경우가 대부분인 것 같다. 

![01-pip-syntax-error](https://github.com/pyeon9/images-for-github-page/blob/main/setup/2021-04/04-25-pip-syntax-error/01-pip-syntax-error.png?raw=true)

이 에러를 해결하기 위해서는 pip를 구버전인 2.7 버전으로 되돌리면 된다. 아래 명령어를 사용하면 된다.
```
  $ curl https://bootstrap.pypa.io/pip/2.7/get-pip.py --output get-pip.py
  $ python get-pip.py
```

<!-- Last modified: 21-04-25, 13:42 -->