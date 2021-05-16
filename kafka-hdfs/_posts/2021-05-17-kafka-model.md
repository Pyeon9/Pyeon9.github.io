---
layout: post
title: "[Kafka] Kafka Python으로 TensorFlow model 전송 및 수신하기"
description: >
  kafka-python으로 tensorflow 2.0의 모델 파일(.h5) 전송하고 수신하는 예제
---

이 글에서는 TensorFlow2의 모델 파일(.h5)을 보내고 받는 예제를 다룬다.    

TensorFlow에서 .h5 형식의 모델을 json으로 변환하고, json 파일로부터 모델을 생성하는 기능을 제공한다. 따라서, .h5 파일을 json 형식으로 변환 후에 utf-8 형식으로 인코딩하여 전달하면 간단하다. 

## 주키퍼 및 카프카 서버 실행
우선 주키퍼와 카프카 서버를 실행해야 한다. 실행하는 방법은 [**이 게시글**](https://pyeon9.github.io/blog/kafka-hdfs/2021-04-21-kafka-example/)을 참고.

## Producer

```python
from kafka import KafkaProducer
from json import dumps
import tensorflow as tf

producer = KafkaProducer(acks=0,
                         bootstrap_servers=['localhost:9092'],
                         value_serializer=lambda x: dumps(x).encode('utf-8')
)

model = tf.keras.models.load_model('./classifier.h5')
print(model)
json_model = model.to_json()

producer.send('model', json_model)
producer.flush()
print('[end] send producer')
```

value_serializer로는 json의 dumps 함수를 사용하고, 'utf-8'로 인코딩 한다.   
.h5 파일을 load한 후, to_json() 함수를 이용하여 json 파일로 변환하여 send한다. 

## Consumer

```python
from kafka import KafkaConsumer
from json import loads
import tensorflow as tf

consumer = KafkaConsumer('model',
                         bootstrap_servers=['localhost:9092'],
                         auto_offset_reset='earliest',
                         enable_auto_commit=True,
                         group_id='model-group',
                         consumer_timeout_ms=1000,
                         value_deserializer=lambda x: loads(x.decode('utf-8'))
)

print('[begin] get consumer list')
for message in consumer:
    print("Topic: %s, Offset: %d, Value: %s" % \
            (message.topic, message.offset, message.value)
    )
    json_model = message.value
print('[end] get consumer list')

model = tf.keras.models.model_from_json(json_model)
print(model)
```

consumer의 value_deserializer는 json의 loads 함수를 사용하고 'utf-8' 형식을 디코딩한다.   
message 수신 후에는 json 형태의 모델을 tf.keras.models의 model_from_json() 함수를 사용하여 모델 객체로 변환하여 준다. 

## 실행 
두 파일을 모두 작성했다면 직접 실행하여 결과를 확인해보자. 우선 producer.py를 실행한다.
```
  $ python producer.py
```

![model-producer](https://github.com/pyeon9/images-for-github-page/blob/main/kafka-hdfs/2021-05/05-17-kafka-model/model-producer.png?raw=true)

모델 객체가 출력되고 전송이 완료됐다고 나타난다.

다음으로 consumer.py를 실행한다.
```
  $ python consumer.py
```

![model-consumer](?raw=true)

json 형태로 모델의 정보가 출력되는 것도 볼 수 있고, 가장 마지막에는 모델 객체로 변환되어 정상적으로 출력이 됨을 확인할 수 있다. 

<br/>

<!-- Last modified: 21-05-17, 00:36 -->