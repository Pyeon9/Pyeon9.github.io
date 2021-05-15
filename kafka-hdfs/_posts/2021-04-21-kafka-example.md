---
layout: post
title: "[Kafka] Kafka Python Producer, Consumer 예제"
description: >
  간단한 Kafka Producer와 Consumer 예제 코드
---

[**CentOS에 Kafka 설치하기**](https://pyeon9.github.io/blog/setup/2021-04-19-CentOS-Kafka-install/)를 통해 주키퍼와 카프카를 설치하였다. 이번에는 간단한 예제를 통해 Producer와 Consumer를 구현해보겠다.

## kafka-python 설치
카프카는 기본적으로 Java를 기반으로 운영되지만, Python으로도 활용이 가능하다. Python에서 카프카를 활용하기 위해서는 `kafka-python`이라는 라이브러리의 설치가 필요하다. 아래 명령어를 통해 설치해주자.
```
  $ pip install kafka-python
```

## 주키퍼 및 카프카 서버 실행
Producer와 Consumer가 역할을 하기 위해서는 주키퍼와 카프카 서버가 실행되어 있어야한다. 아래 명령어를 통해서 주키퍼와 카프카 서버를 실행한다. 경로는 본인의 설치 경로에 맞게 대체하면 되고, -daemon 옵션은 프로세스를 백그라운드에서 실행할 수 있게 해준다.
```
  $ /주키퍼 설치 경로/zookeeper/bin/zkServer.sh start
  $ /카프카 설치 경로/kafka/bin/kafka-server-start.sh -daemon /카프카 설치 경로/kafka/config/server.properties
```

아래 명령어를 통해서 주키퍼와 카프카 서버가 정상적으로 실행되었는지 확인할 수 있다. LISTEN으로 나오면 정상적으로 실행이 된 것이다.

![01-netstat](https://github.com/pyeon9/images-for-github-page/blob/main/kafka-hdfs/2021-04/04-21-kafka-example/01-netstat.png?raw=true)


## Producer
주키퍼와 카프카 서버를 실행하였으면 아래와 같이 producer.py 파일을 생성한다. 

```python
from kafka import KafkaProducer 
from json import dumps 
import time 

producer = KafkaProducer(acks=0, compression_type='gzip', bootstrap_servers=['localhost:9092'], value_serializer=lambda x: dumps(x).encode('utf-8')) 
start = time.time() 
for i in range(1000): 
    data = {'str' : 'result'+str(i)} 
    producer.send('test', value=data) 
    producer.flush() 
print("elapsed :", time.time() - start)
```

producer는 json 형태로 data를 생성하고, i 값을 1000까지 증가시키며 'result'라는 문자열과 합하여 `test` 토픽으로 전달한다.

## Consumer
producer 파일을 작성한 후에는 consumer.py 파일을 작성한다. producer.py와 다른 pc에서 consume하고 싶다면 bootstrap_servers에 localhost 대신 카프카 서버의 IP를 입력하면 된다.

```python
from kafka import KafkaConsumer
from json import loads

consumer = KafkaConsumer(
    'test',
    # server's address (inference)
    bootstrap_servers=['localhost:9092'],
    auto_offset_reset='earliest',
    enable_auto_commit=True,
    group_id='my-group',
    value_deserializer=lambda x: loads(x.decode('utf-8')),
    consumer_timeout_ms=1000
)

# get consumer list
print('[begin] get consumer list')
for message in consumer:
    print("Topic: %s, Partition: %d, Offset: %d, Key: %s, Value: %s" % \
            (message.topic, message.partition, message.offset, message.key, \
                message.value)
    )
print('[end] get consumer list')
```

consumer는 `test` 토픽으로부터 data를 가져와 토픽 이름, 파티션 번호, 데이터 등의 정보를 출력한다.

## 실행 
두 파일을 모두 작성했다면 직접 실행하여 결과를 확인해보자. 우선 producer.py를 실행한다.
```
  $ python producer.py
```

![02-producer](https://github.com/pyeon9/images-for-github-page/blob/main/kafka-hdfs/2021-04/04-21-kafka-example/02-producer.png?raw=true)

총 1,000개의 데이터를 전송하는데 0.63초 정도가 소요되었다.   
이제 consumer.py를 실행해보자.
```
  $ python consumer.py
```

![03-consumer](https://github.com/pyeon9/images-for-github-page/blob/main/kafka-hdfs/2021-04/04-21-kafka-example/03-consumer.png?raw=true)

producer로 보냈던 data들이 출력되는 것을 확인할 수 있다.

<br/>

이 내용은 [이곳](https://needjarvis.tistory.com/607)을 일부 참고하였다.

<!-- Last modified: 21-04-21, 21:29 -->