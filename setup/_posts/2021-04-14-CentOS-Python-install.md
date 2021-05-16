---
layout: post
title: "[CentOS] CentOS에 Python 3 설치하기"
description: >
    CentOS 7에 Python 3.x 버전 설치하는 방법.
---

CentOS 7에는 기본적으로 Python 2가 내장되어 있다.
아래 명령어를 통해서 현재 설치되어 있는 버전을 확인할 수 있다.
```
  $ python -V
  $ python --version
```

![python-version-check](https://github.com/pyeon9/images-for-github-page/blob/main/setup/2021-04/04-14-CentOS-Python-install/01-python-version-check.png?raw=true)

내 환경에서는 2.7.5 버전이 설치되어 있다.
그러나 요즘 Python 2로 개발하는 경우는 많지 않을 것이다. 나 또한 Python 3.x 버전에서 개발을 할 것이므로, 이번엔 Python 3.8.5 버전을 설치하는 방법을 알아본다.

우선 아래 명령어를 통해 필요한 플러그인을 설치한다. 
```
  $ yum install gcc openssl-devel bzip2-devel libffi-devel
```
중간에 아래와 같이 어느 정도의 용량을 차지한다고 설치해도 괜찮겠냐고 묻는 메시지가 나온다면 'y'를 입력하고 엔터를 눌러 설치를 진행한다. 

![plugin](https://github.com/pyeon9/images-for-github-page/blob/main/setup/2021-04/04-14-CentOS-Python-install/02-python-plugin.png?raw=true)

아래와 같이 나온다면 성공.

![plugin-complete](https://github.com/pyeon9/images-for-github-page/blob/main/setup/2021-04/04-14-CentOS-Python-install/03-python-plugin-complete.png?raw=true)


다음으로 [Python 홈페이지](https://www.python.org/downloads/)에서 원하는 버전을 선택한다. 버전을 선택했다면 우선 다운받을 폴더로 이동한다. 그 후 아래 명령어에서 버전만 원하는 버전으로 바꾸어 설치 파일을 다운받는다. 참고로 wget은 웹 서버로부터 파일을 다운받을 때 사용하는 명령어이다. 
```
  $ wget https://www.python.org/ftp/python/3.8.5/Python-3.8.5.tgz
```

다운이 완료되면 압축을 풀어준다.  압축을 풀면 버전 이름의 폴더가 생긴다.
```
  $ tar xzf Python-3.8.5.tgz
```

![python-directory](https://github.com/pyeon9/images-for-github-page/blob/main/setup/2021-04/04-14-CentOS-Python-install/04-python-directory.png?raw=true)

이제 해당 폴더로 이동하여 configure 파일을 실행하여 컴파일을 진행한다. --enable-optimizations 옵션은 말 그대로 최적화된 빌드를 하겠다는 의미다.
```
  $ cd Python-3.8.5
  $ ./configure --enable-optimizations
```

컴파일이 끝나면 아래 명령어를 통해 설치를 진행한다. 컴파일보다는 긴 시간이 걸리지만 기다리면 설치가 완료된다. 
```
  $ make altinstall
```

설치가 완료되면 아래 명령어로 새로 설치한 python 3.8 버전의 위치를 확인할 수 있다. 설치가 무사히 완료되었다면 아래 그림과 같은 출력을 얻을 것이다.
```
  $ which python3.8
```

![which-python](https://github.com/pyeon9/images-for-github-page/blob/main/setup/2021-04/04-14-CentOS-Python-install/05-which-python.png?raw=true)

다시 한 번 python의 버전을 확인해보자.
```
  $ python -V
  $ python -- version
```

![python-version](https://github.com/pyeon9/images-for-github-page/blob/main/setup/2021-04/04-14-CentOS-Python-install/06-python-version-check.png?raw=true)

python 3.8 버전을 설치했지만 기존에 설치되어 있던 2.7.5 버전을 출력한다. 아래와 같이 python3.8의 버전을 확인해야만 우리가 설치했던 python 3.8.5 버전을 확인할 수 있다.

![python3.8-version](https://github.com/pyeon9/images-for-github-page/blob/main/setup/2021-04/04-14-CentOS-Python-install/07-python3.8-version-check.png?raw=true)

이는 기존에 설치되어 있던 python 2.7.5가 기본으로 설정되어 있기 때문인데, python 2.7.5 버전은 사용하지 않을 것이므로 기본 python을 새로 설치한 python 3.8.5로 변경하겠다.
우선 각자 편한 편집기를 사용하여 .bashrc 파일을 오픈한다. 나는 vi 편집기를 사용하였다.
```
  $ vi /root/.bashrc
```

i를 눌러 편집 모드로 변경하고, 아래 문장을 입력한 후 ESC를 눌러 명령모드로 나간 후 :wq로 편집한 내용을 기록하고 종료한다. 위에서 which python3.8을 했을 때 출력된 경로를 입력해주면 된다.

alias는 가명, 별칭 등의 뜻으로, python이라는 이름이 " " 사이의 경로를 나타내게 한다. 따라서, " " 사이에 새로 설치한 python 3.8.5의 경로를 넣어주면 python을 입력했을 때 python 3.8.5를 기본으로 사용하게 된다. 

![python-alias](https://github.com/pyeon9/images-for-github-page/blob/main/setup/2021-04/04-14-CentOS-Python-install/08-python-alias.png?raw=true)

편집을 끝내면 아래 명령어를 통해 변경 사항을 적용해준다.
```
  $ source /root/.bashrc
```

마지막으로 다시 아래 명령어를 입력했을 때 새로 설치한 python 3.8.5가 나타나는지 확인한다. python 2.7.5에서 python 3.8.5로 바뀐 것을 확인할 수 있다.

![python-version-changed](https://github.com/pyeon9/images-for-github-page/blob/main/setup/2021-04/04-14-CentOS-Python-install/09-python-version-changed.png?raw=true)

혹시 여전히 2.7.5 버전으로 표시된다면 .bashrc 파일의 alias가 잘못 되었을 확률이 높다. 나와 다른 경로에 python 3 버전이 설치되었을 수도 있으니 직접 which 명령어를 통해 자신의 컴퓨터에 python 3가 설치된 경로를 확인하고 .bashrc에 작성하면 될 것이다. 



<!-- Last modified: 21-04-15, 10:23 -->