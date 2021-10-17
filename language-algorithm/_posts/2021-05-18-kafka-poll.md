---
layout: post
title: "[Kafka] Kafka Python consumer poll() method 예제"
description: >
  poll() 함수를 사용하여 message를 consume하는 kafka-python 예제
---

[kafka-python 예제](https://pyeon9.github.io/blog/kafka-hdfs/2021-04-21-kafka-example/)에서 producer로 토픽에 데이터를 보내 놓고 consumer로 받는 것을 해보았다. 이 예제에서는 producer가 우선하여 데이터를 전송해야 consumer를 실행했을 때 데이터를 받을 수 있었다.    

이번에는 consumer를 실행시켜 놓고 producer가 토픽에 데이터를 전송하면 즉시 받아오는 방법을 알아보겠다. 이는 while문 안에서 consumer의 [poll() method](https://kafka-python.readthedocs.io/en/master/apidoc/KafkaConsumer.html#kafka.KafkaConsumer.poll)를 사용하여 할 수 있다. `poll()`은 가장 마지막으로 consume한 offset부터 새로운 메세지가 들어오면 그 메세지를 가져다준다. `timeout_ms` 인자는 새로운 데이터가 없을 때 기다릴 시간을 의미한다.

## Producer

```python
from kafka import KafkaProducer
import time

producer = KafkaProducer(acks=0, compression_type='gzip', \
                        bootstrap_servers=['localhost:9092'], \
                        value_serializer=lambda x: x.encode('utf-8')
)

start = time.time()
for i in range(10):
    data = str(i)
    producer.send('poll', value=data)
    producer.flush()
print('elapsed :', time.time() - start)
```

producer는 string을 'utf-8' 형식으로 인코딩하도록 value_serializer를 설정하여 생성한다. 이 예제에서는 string으로 형변환된 0~9의 숫자를 'utf-8'로 인코딩하여 'poll' 토픽으로 전송한다. 

## Consumer

```python
from kafka import KafkaConsumer
from time import sleep

consumer = KafkaConsumer('poll',
                         bootstrap_servers=['localhost:9092'],
                         auto_offset_reset='earliest',
                         enable_auto_commit=True,
                         group_id='pickle-group',
                         consumer_timeout_ms=1000,
                         value_deserializer=lambda x: x.decode('utf-8')
)

while True:
    batch = consumer.poll(timeout_ms=1000)
    if len(batch.items()) > 0:
        print('## Message Received!')
        for partition, messages in batch.items():
            for message in messages:
                print("Topic: %s, Offset: %d, Value: %s" % \
                    (message.topic, message.offset, message.value)
                )
    else:
        print('No messages')
        sleep(3)
```

consumer는 'utf-8'로 인코딩된 데이터를 디코딩하도록 value_deserializer를 설정하여 생성한다.   
while문 안에서 consumer는 'poll' 토픽에게 `poll()`을 시도하고, 메세지가 있다면(if 조건) 메세지를 출력한다. 그러나 새로운 메세지가 없는 경우 'No messages'를 출력하고 3초 대기한 후 다시 `poll()`을 시도한다. 

## 실행 
직접 실행한 결과를 살펴보자. 우선 consumer를 실행한다.
```
  $ python consumer.py
```

![consumer-waiting](https://github.com/pyeon9/images-for-github-page/blob/main/kafka-hdfs/2021-05/05-18-kafka-poll/01-consumer-waiting.png?raw=true)

처음에는 전송한 메세지가 없으므로 'No messages'가 3초 간격으로 출력된다.    

이제 producer를 실행하여 메세지를 전송해보자.

```
  $ python producer.py
```

![consumer-first-poll](https://github.com/pyeon9/images-for-github-page/blob/main/kafka-hdfs/2021-05/05-18-kafka-poll/02-consumer-fisrt-poll.png?raw=true)

producer가 메세지를 전송한 후 '## Message Received!'라는 문장과 함께 0~9까지의 값이 출력된다. 그 후 새롭게 추가된 메세지가 없어 다시 'No message'가 출력된다.   

여기서 다시 한 번 producer를 실행하면 다음과 같다.

![consumer-second-poll](https://github.com/pyeon9/images-for-github-page/blob/main/kafka-hdfs/2021-05/05-18-kafka-poll/03-consumer-second-poll.png?raw=true)

0~9까지의 값이 다시 출력된다. 앞서와 같은 값이 들어오므로 앞선 값을 다시 읽었다고 생각할 수도 있지만 그렇지 않다. 앞서 offset 9까지 읽었는데, consumer의 auto commit으로 인해 이 정보가 기록되어 있어 같은 값을 읽어온 것이 아니고 offset 10부터 새로 들어온 값을 읽어온 것이다.

<br/>

이번에는 consumer는 실행된 상태로 producer가 메세지를 보내면 자동으로 받아오는 예제를 알아보았다. 실제로는 이런 식으로 while문 안에서 `poll()`을 하고, 조건문을 통해 활용하는 경우가 더 많을텐데, 도움이 되었으면 좋겠다.

<!-- Last modified: 21-05-18, 11:35 -->