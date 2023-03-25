# confluent-kafka-python

This repository for keeping code from Introduction to [Python for Apache Kafka](https://developer.confluent.io/learn-kafka/kafka-python/intro)

# Table of contents

- [Setup Kafka](#setup-kafka)
- [Producer](#producer)
    - [Hands on](##hands-on--use-producer-to-send-events-to-kafka-)
- Consumer
- Schema-registry
- Serializers/ Deserilalizers
    - Protobuf
    - JSON Schema
    - Avro
- AdminClient

# Setup ( TODO )

## Kafka cluster
Create config file

## Client python

```bash
poetry install
```

# Producer
## producer responsibility

![Alt text](images/producer.JPG)

## producer configuration

![Alt text](images/producer-configuration.JPG)

## producing data

![Alt text](images/producing-data.JPG)

Example python

**Asynchronous-writes**

https://docs.confluent.io/kafka-clients/python/current/overview.html#asynchronous-writes

```python
for i in range(10):
    producer.produce(topic='my_topic', val=f'msg #{i}', key=str(i),
                    on_delivery=callback, header={'foo':'bar'})

producer.flush()
```

**Synchronous-writes**

https://docs.confluent.io/kafka-clients/python/current/overview.html#asynchronous-writes

```python
for i in range(10):
    producer.produce(topic='my_topic', val=f'msg #{i}', key=str(i),
                    on_delivery=callback, header={'foo':'bar'})
                    
producer.pull(10)
```

## producer method
https://github.com/confluentinc/confluent-kafka-python/blob/master/examples/eos-transactions.py

![Alt text](images/producing-method.JPG)

# Hands On ( Use Producer to Send Events to Kafka )

## Create Topics

1. Navigate to `controlcenter.cluster` at control-center ( http://localhost:9021/ )

<img src="images/producer-handson.JPG"  width="320" height="280">

2. Create a new topic called `hello_topic`, with `6` partitions and defaults settings.

## Project Setup

1. Start a virtual environment
```bash
poetry shell
```

## Create producer.py

1. Create `producer.py` at `src/producer.py`

2. Add Required Imports

```python
# producer.py

from confluent_kafka import Producer
from config import config
```

3. Create a function called callback() that can be passed to the produce() method.

```python
def callback(err, event):
    if err:
        print(f'Produce to topic {event.topic()} failed for event: {event.key()}')
    else:
        val = event.value().decode('utf8')
        print(f'{val} sent to partition {event.partition()}.')
```

4. Create a function called say_hello() that takes a producer and a key.
```python
def say_hello(producer, key):
    value = f'Hello {key}!'
    producer.produce('hello_topic', value, key, on_delivery=callback)
```

5. Add the following main block that will pull this all together
```python
if __name__ == '__main__':
    producer = Producer(config)
    keys = ['Amy', 'Brenda', 'Cindy', 'Derrick', 'Elaine', 'Fred']
    [say_hello(producer, key) for key in keys]
    producer.flush()
```

## Run the Program
```bash
python producer.py
```

Notice how the different names, which we are using for keys, result in specific partition assignments. To get a better idea of how this works, you can try changing some of the names and see how the partition assignment changes.