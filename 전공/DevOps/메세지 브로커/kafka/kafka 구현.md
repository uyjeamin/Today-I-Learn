
# kafka 도커에 띄우기






# Topic 생성

1. 카프카 bash 로 접속
```bash

version: '3'

services:
  zookeeper:
    image: confluentinc/cp-zookeeper:7.3.0
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
    ports:
      - 2181:2181

  kafka1:
    container_name: kafka1
    image: confluentinc/cp-kafka:7.3.0
    ports:
      - 9092:9092
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: PLAINTEXT:PLAINTEXT
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka1:9092
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 2
    depends_on:
      - zookeeper

  kafka2:
    container_name: kafka2
    image: confluentinc/cp-kafka:7.3.0
    ports:
      - 9093:9093
    environment:
      KAFKA_BROKER_ID: 2
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: PLAINTEXT:PLAINTEXT
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka2:9093
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 2
    depends_on:
      - zookeeper

```


2. topic 생성
```bash
  kafka-topics --create \ 
  --bootstrap-server localhost:9092 \ # 브로커중 하나에 접속 = 클러스터에 접속됨
  --replication-factor 2 \ # 파티션 내용을 복제할 브로커(follower) 수 지정(현재 1개) , 1로하면 leader 밖에 없음
  --partitions 3 \ # 파티션 수
  --topic chat-topic # 토픽 이름
```

설명 없는 버전
```bash
  kafka-topics --create \
  --bootstrap-server study-kafka-kafka2-1:9093 \
  --replication-factor 2 \
  --partitions 3 \
  --topic hello-topic

```