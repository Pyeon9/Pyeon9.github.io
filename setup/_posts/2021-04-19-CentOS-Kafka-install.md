---
layout: post
title: "[CentOS] CentOS에 Kafka 설치하기"
description: >
    CentOS 7에 Apache Kafka 설치하는 방법 소개.
---

아파치 카프카는 비동기 통신을 큰 규모와 빠른 속도로 처리할 수 있게 개발된 애플리케이션이다. 이 포스트에서는 카프카를 CentOS 7에 설치하는 방법을 알아본다.

## JAVA 설치
카프카는 자바를 기반으로 하는 애플리케이션이기 때문에, 카프카를 운영하려는 서버에 자바가 설치되어 있어야 한다. 따라서 자바를 설치하는 방법을 우선으로 알아본다. 

먼저, [오라클](https://www.oracle.com/java/technologies/javase-downloads.html) 홈페이지에 접속해 다운받을 JDK 버전을 선택한다. 버전을 선택했다면 아래처럼 **JDK Download**를 통해 다운로드 사이트로 이동한다. 

![01-java-version](https://github.com/pyeon9/images-for-github-page/blob/main/setup/2021-04/04-19-CentOS-Kafka-install/01-java-version.png?raw=true)

이제 서버에 맞는 파일을 다운받으면 되는데, CentOS에 설치하기 위해서는 아래 표시한 파일을 다운 받으면 된다.

![02-java-download](https://github.com/pyeon9/images-for-github-page/blob/main/setup/2021-04/04-19-CentOS-Kafka-install/02-java-download.png?raw=true)

다운받은 파일을 설치를 원하는 폴더로 이동한다. 그리고 아래와 같이 파일 모드를 변경한 후 설치를 실행한다. 참고로 +x는 실행 권한을 부여해준다는 뜻이다.
```
  $ chmod +x jdk-16_linux-x64_bin.rpm
  $ rpm -ivh jdk-16_linux-x64_bin.rpm
```
그러면 잠시 후 설치가 완료될 것이다. 마지막으로 JAVA_HOME 환경 변수를 추가해주어야 한다. 아래 명령어를 통해 /etc/profile에 JAVA_HOME 환경 변수를 출력한다.
```
  $ echo "export JAVA_HOME=/usr/java/jdk-16" >> /etc/profile
```

## 주키퍼(zookeeper) 설치 
주키퍼는 카프카와 같은 분산 애플리케이션을 위한 코디네이션(coordination) 시스템이다. 분산 애플리케이션이 안정적인 서비스를 할 수 있도록 분산되어 있는 각 애플리케이션의 정보를 중앙에 집중하고 구성 관리, 그룹 관리 네이밍, 동기화 등의 서비스를 제공한다. 카프카가 주키퍼를 코디네이션 로직으로 이용하고 있으므로 주키퍼를 우선 설치해본다.

[주키퍼 다운로드](https://zookeeper.apache.org/releases.html#download)에 들어가 다운로드 할 버전을 선택한다. 선택한 버전의 링크를 클릭하면 아래와 같은 사이트가 뜰 것이다. 아래처럼 화면에 나타난 주소를 복사한 다음, 설치를 원하는 폴더로 이동해 wget 명령어로 파일을 다운 받는다.
```
  $ wget https://downloads.apache.org/zookeeper/zookeeper-3.7.0/apache-zookeeper-3.7.0-bin.tar.gz
```

![03-zookeeper-download](https://github.com/pyeon9/images-for-github-page/blob/main/setup/2021-04/04-19-CentOS-Kafka-install/03-zookeeper-download.png?raw=true)

그 후엔, tar 명령어로 압축을 풀어준다.
```
  $ tar zxg apache-zookeeper-3.7.0-bin.tar.gz
```

압축을 풀고 나서 ls 명령어를 입력해보면, `apache-zookeeper-3.7.0-bin` 폴더를 확인할 수 있는데, 버전 정보까지 포함하고 있어 그대로 사용하기보다는 심볼링 링크를 만들어 사용하는 것이 편리하다. 또한, 추후 버전 변경 시 발생하는 문제들도 방지할 수 있다. 아래와 같이 심볼링 링크를 생성한다.
```
  $ ln -s apache-zookeeper-3.7.0-bin zookeeper
```
아래 명령어로 심볼릭 링크를 확인했을 때 그림처럼 나오면 성공한 것이다.
```
  $ ls -la zookeeper
```

![04-symbolic-link](https://github.com/pyeon9/images-for-github-page/blob/main/setup/2021-04/04-19-CentOS-Kafka-install/04-symbolic-link.png?raw=true)

### 환경 설정
카프카 서버를 정상적으로 운영하려면 몇 가지 설정을 해줘야 한다. 지금 자세히 이해할 필요는 없으니 우선 아래를 따라하자.
1. 스냅샷과 트랜잭션 로그를 저장할 디렉토리 생성
```
  $ cd zookeeper
  $ mkdir -p ./data
```

2. 앙상블 내 주키퍼 노드 구분 ID 만들기 
```
  $ echo 1 > ./data/myid
```
 
3. 주키퍼 환경 설정 파일 생성
```
  $ vi ./conf/zoo.cfg
```
위 명령어로 zoo.cfg 파일을 만든 후 아래 내용을 작성하고 저장한다.

![05-zoo-cfg](https://github.com/pyeon9/images-for-github-page/blob/main/setup/2021-04/04-19-CentOS-Kafka-install/05-zoo-cfg.png?raw=true)

마지막으로 아래 명령어를 통해 주키퍼가 정상적으로 실행되는지 확인한다.
```
  $ ./bin/zkServer.sh start
```
아래와 같이 뜬다면 성공한 것이며, 중지는 start를 stop으로 바꿔 입력하면 된다.

![06-zookeeper-start](https://github.com/pyeon9/images-for-github-page/blob/main/setup/2021-04/04-19-CentOS-Kafka-install/06-zookeeper-start.png?raw=true)

## 카프카 설치 
주키퍼 설정까지 완료했으니 이제 카프카를 설치해보자. 주키퍼 설치와 마찬가지로 우선 [다운로드](http://kafka.apache.org/downloads)페이지에 가서 다운로드할 버전을 선택한다. 선택한 버전의 링크를 클릭하면 앞서 보았던 미러 사이트가 나올 것이다. 설치를 원하는 폴더로 이동 후, 다시 한번 주소를 복사하여 wget 명령어로 파일을 다운 받는다.

![07-kafka-download](https://github.com/pyeon9/images-for-github-page/blob/main/setup/2021-04/04-19-CentOS-Kafka-install/07-kafka-download.png?raw=true)

아래 명령어로 압축을 풀자.
```
  $ tar zxf kafka_2.13-2.7.0.tgz
```
압축을 풀고 ls 명령어를 입력하면 `kafka-2.7.0-src`와 같이 버전 정보가 포함된 폴더가 생성이 된다. 주키퍼와 마찬가지로, 편의와 관리의 효율을 위하여 아래와 같이 심볼릭 링크를 생성한다.
```
  $ ln -s kafka_2.13-2.7.0 kafka
  $ ls -la kafka
```

![08-kafka-symbolic-link](https://github.com/pyeon9/images-for-github-page/blob/main/setup/2021-04/04-19-CentOS-Kafka-install/08-kafka-symbolic-link.png?raw=true)

### 환경 설정
주키퍼와 마찬가지로, 카프카 또한 몇 가지 환경 설정이 필요하다. 설치 및 실행을 소개하는 이 글의 특성 상, 세부적인 설명은 생략한다. 

1. 저장 디렉토리 생성
```
  $ cd kafka
  $ mkdir -p ./data
```

2. 환경 설정 파일 수정
각자 편한 편집기를 활용하여 kafka/config/server.properties 파일을 열고, 아래 내용을 찾아 수정한다.

- broker.id=1   
- log.dirs=/ex_data/kafka/data   
- zookeeper.connect=localhost:2181   

<br/>
여기까지 완료했으면 모든 준비는 끝났다. 이제 카프카를 실행해보겠다. 아래와 같이 나온다면 성공.
```
  $ ./bin/kafka-server-start.sh ./config/server.properties
```

![09-kafka-started](https://github.com/pyeon9/images-for-github-page/blob/main/setup/2021-04/04-19-CentOS-Kafka-install/09-kafka-started.png?raw=true)

### 카프카 상태 확인
주키퍼와 카프카를 설치 후 실행했는데, 제대로 실행되고 있는지 다른 방법으로 확인해보겠다.
주키퍼와 카프카 설치 시 기본 TCP 포트를 사용하도록 되어 있는데, 주키퍼의 기본 포트는 2181이고, 카프카의 기본 포트는 9092이다. 아래 명령어를 통해 주키퍼와 카프카가 리스닝 상태인지 확인한다. 포트가 리스닝 상태라는 것은 애플리케이션이 TCP를 사용하는 서버에서 실행중이고 다른 컴퓨터와 연결될 때까지 기다린다는 뜻이다. 
```
  $ netstat-ntlp | grep 2181
  $ netstat-ntlp | grep 9092
```
둘 다 LISTEN으로 떴다면 주키퍼와 카프카가 정상적으로 실행되고 있는 것이다. 

<br/>
주키퍼와 카프카의 기반인 자바와, 주키퍼 및 카프카를 설치하는 과정을 정리하였다. 주키퍼와 카프카는 설치 뿐만 아니라 여러가지 환경 설정들도 필요한데, 이는 사용자의 환경 및 목적에 따라 다를뿐더러 개념적인 내용들을 알고 있어야 해 이 글에서는 자세하게 소개하지 않았다. 추후에 나의 환경을 완성하게 되거나 자유자재로 원하는 환경을 구성할 수 있게 되면 방법을 공유하도록 하겠다. 


<!-- Last modified: 21-04-21, 17:01 -->