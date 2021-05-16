---
layout: post
title: "[Kafka] Kafka Python으로 pickle 파일 전송 및 수신하기"
description: >
  kafka-python으로 python pickle 파일 전송하고 수신하는 예제
---

[**이전 게시글**](https://pyeon9.github.io/blog/kafka-hdfs/2021-04-21-kafka-example/)에서 Kafka Python의 producer 및 consumer 예제를 간단하게 다뤘다.   
오늘은 pickle 파일을 보내고 받는 예제를 보겠다.

## 주키퍼 및 카프카 서버 실행
우선 주키퍼와 카프카 서버를 실행해야 한다. 이전 게시글에서도 다뤘듯이, 아래와 같은 방법으로 실행한다.   

아래 명령어를 통해서 주키퍼와 카프카 서버를 실행한다. 경로는 본인의 설치 경로에 맞게 대체하면 되고, -daemon 옵션은 프로세스를 백그라운드에서 실행할 수 있게 해준다.
```
  $ /주키퍼 설치 경로/zookeeper/bin/zkServer.sh start
  $ /카프카 설치 경로/kafka/bin/kafka-server-start.sh -daemon /카프카 설치 경로/kafka/config/server.properties
```

아래 명령어를 통해서 주키퍼와 카프카 서버가 정상적으로 실행되었는지 확인할 수 있다. LISTEN으로 나오면 정상적으로 실행이 된 것이다.

![netstat](https://github.com/pyeon9/images-for-github-page/blob/main/kafka-hdfs/2021-04/04-21-kafka-example/01-netstat.png?raw=true)


## Producer
pickle 파일을 전송하는 여러가지 방법이 있겠지만, 이 글에서는 pickle 파일을 bytes로 변환하여 전송하도록 하겠다. 내가 사용한 pickle 파일이 numpy array를 담고 있어 bytes로의 변환, 다시 numpy array로의 변환이 매우 쉽기 때문이다. 

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

tobytes() 함수로 value_serializer를 설정하여 producer를 생성한다. pickle 파일을 load 후 producer로 send()하면 value_serializer가 bytes로 변환하여 전송해준다. 


## Consumer
consumer는 bytes를 받아 다시 numpy array로 변환해준다

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

producer와는 반대로 numpy.frombuffer() 함수로 value_deserializer를 설정하여 consumer를 생성한다. numpy의 frombuffer() 함수는 bytes를 numpy array로 변환하여준다. value_deserializer에 의해 message의 value는 numpy array로 변환된다.

## 실행 
두 파일을 모두 작성했다면 직접 실행하여 결과를 확인해보자. 우선 producer.py를 실행한다.
```
  $ python producer.py
```

![pickle-producer](https://github.com/pyeon9/images-for-github-page/blob/main/kafka-hdfs/2021-05/05-16-kafka-pickle/pickle-producer.png?raw=true)

label이 출력되고 전송이 완료된 것으로 나타난다.

다음으로 consumer.py를 실행한다.
```
  $ python consumer.py
```

![pickle-consumer](https://github.com/pyeon9/images-for-github-page/blob/main/kafka-hdfs/2021-05/05-16-kafka-pickle/pickle-consumer.png?raw=true)

label이 value로 출력되는 것을 확인할 수 있다. 전송 전/후로 label의 shape이 다른데, 이는 tobytes() 함수와 numpy.frombuffer() 함수의 동작 때문이다. 수신 후 원하는 shape으로 바꾸어 사용하면 된다. 

<br/>

<!-- Last modified: 21-05-16, 17:52 -->