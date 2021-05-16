---
layout: post
title: "[Kafka] Kafka Python으로 pickle 파일 전송하기"
description: >
  Kafka Python으로 pickle 파일을 전송하고 수신하는 예제
---

[**지난 게시글**](https://pyeon9.github.io/blog/kafka-hdfs/2021-04-21-kafka-example/)에서 간단한 Kafka Python의 Producer와 Consumer를 구현해보았다. 이 게시글에서는 python의 pickle 파일을 전송하고 수신하는 방법을 살펴보겠다.


## 주키퍼 및 카프카 서버 실행
우선 주키퍼와 카프카 서버를 실행해둔다. 방법은 지난 게시글에도 실려있듯이 아래와 같다.   

아래 명령어를 통해서 주키퍼와 카프카 서버를 실행한다. 경로는 본인의 설치 경로에 맞게 대체하면 되고, -daemon 옵션은 프로세스를 백그라운드에서 실행할 수 있게 해준다.
```
  $ /주키퍼 설치 경로/zookeeper/bin/zkServer.sh start
  $ /카프카 설치 경로/kafka/bin/kafka-server-start.sh -daemon /카프카 설치 경로/kafka/config/server.properties
```

아래 명령어를 통해서 주키퍼와 카프카 서버가 정상적으로 실행되었는지 확인할 수 있다. LISTEN으로 나오면 정상적으로 실행이 된 것이다.

![01-netstat](https://github.com/pyeon9/images-for-github-page/blob/main/kafka-hdfs/2021-04/04-21-kafka-example/01-netstat.png?raw=true)


## Producer
pickle 파일을 Kafka를 통해 전송하는 데는 여러가지 방법이 있을 수 있다. 내가 시도한 방법은 pickle 파일을 bytes로 바꿔서 전송하는 방법이다.   
내가 이 예제에서 사용한 pickle 파일은 numpy array를 담고 있는데, numpy는 bytes를 numpy array로 바꿔주는 함수를 제공하고 있어서 이 방법이 쉬웠다.

```python
from kafka import KafkaProducer
import gzip
import pickle

producer = KafkaProducer(acks=0,
                         bootstrap_servers=['localhost:9092'],
                         value_serializer=lambda x: x.tobytes()
)

with gzip.open('./label', 'rb') as f:
    label = pickle.load(f)
print(label)

producer.send('pickle', label)
producer.flush()
print('[end] send producer')
```

producer를 생성할 때 입력을 bytes로 바꿔주는 tobytes() 함수로 value_serializer를 설정한다. pickle 파일을 load한 후 producer로 send하면 value_serializer에 의해 pickle 파일의 내용물(여기서는 numpy array)이 bytes로 변환되어 전송된다.

## Consumer
Consumer는 토픽으로부터 메세지를 받아 출력한다. 

```python
from kafka import KafkaConsumer
import numpy as np

consumer = KafkaConsumer('pickle',
                         bootstrap_servers=['localhost:9092'],
                         auto_offset_reset='earliest',
                         enable_auto_commit=True,
                         group_id='pickle-group',
                         consumer_timeout_ms=1000,
                         value_deserializer=lambda x: np.frombuffer(x)
)

print('[begin] get consumer list')
for message in consumer:
    print("Topic: %s, Offset: %d, Value: %s" % \
            (message.topic, message.offset, message.value)
    )
print('[end] get consumer list')
```

consumer는 bytes를 numpy array로 바꿔주는 numpy.frombuffer() 함수를 value_deserializer로 설정하여 생성한다.  bootstrap_servers 항목은 다른 pc에서 consume할 경우 localhost 대신 kafka 서버의 IP를 넣어주면 된다.   
value_deserializer의 동작 덕분에 별다른 작업 없이 message.value를 출력해도 원본 numpy array가 출력된다.

## 실행 
두 파일을 모두 작성했다면 직접 실행하여 결과를 확인해보자. 우선 producer.py를 실행한다.
```
  $ python producer.py
```

![02-producer](https://github.com/pyeon9/images-for-github-page/blob/main/kafka-hdfs/2021-05/05-16-kafka-pickle/pickle-producer.png?raw=true)

label array [[0.1] [0.1] ... [0.9]]가 출력되고 전송이 완료되었다고 나온다.

이제 consumer.py를 실행해보자.
```
  $ python consumer.py
```

![03-consumer](https://github.com/pyeon9/images-for-github-page/blob/main/kafka-hdfs/2021-05/05-16-kafka-pickle/pickle-consumer.png?raw=true)

앞서 보았던 label array가 value로 출력됨을 확인할 수 있다. 보내기 전과 후의 shape이 다른 것은 bytes로 변환하고 numpy.frombuffer() 함수를 통해 다시 numpy array로 변환하는 과정 때문이다. 필요시 consume 후 원하는 shape으로 reshape하여 사용하면 된다. 

<br/>

<!-- Last modified: 21-05-16, 16:43 -->