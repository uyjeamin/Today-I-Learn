순서대로 따라오셈


# kafka 도커에 띄우기

```bash
version: '3'

  
services:
  zookeeper:
    image: confluentinc/cp-zookeeper:7.3.0
    container_name: zookeeper
    ports:
      - 2181:2181
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181


  kafka1:
    container_name: kafka1
    image: confluentinc/cp-kafka:7.3.0
    ports:
      - 9092:9092
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_LISTENERS: PLAINTEXT://0.0.0.0:9092
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://localhost:9092
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: PLAINTEXT:PLAINTEXT
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 2
      KAFKA_LOG_DIRS: /var/lib/kafka/data
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
      KAFKA_LISTENERS: PLAINTEXT://0.0.0.0:9093
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://localhost:9093
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: PLAINTEXT:PLAINTEXT
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 2
      KAFKA_LOG_DIRS: /var/lib/kafka/data
    depends_on:
      - zookeeper

```

# compose 파일 실행

```bash
docker-compose up
```

# compose 파일에 정의된 모든컨테이너 삭제 (볼륨까지)
```bash
docker-compose down -v
```


# producer, consumer 공통 application.yml 파일 (각각 필요한 부분만 분리해도 됨)

```yml
spring:
  kafka:
  # 처음 접속할 브로커 주소들(많이 한 이유는 연결 실패시 다른 브로커에게 접속하기 위해서)
    bootstrap-servers: localhost:9092,localhost:9093
    consumer:
    # 소비자 그룹 id (같은거 끼리 묶음)
      group-id: my-group
    # 처음 메세지를 읽을때 어디부터 읽을건지
      auto-offset-reset: earliest #처음부터


# key, value 값 어떻게 직렬화 할건지 (여기선 String -> byte 으로 역직렬화)
    producer:
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.apache.kafka.common.serialization.StringSerializer


# key, value 값 어떻게 역직렬화 할건지 (여기선 byte -> String 으로 역직렬화)
      key-deserializer: org.apache.kafka.common.serialization.StringDeserializer 
      value-deserializer: org.apache.kafka.common.serialization.StringDeserializer

```


# Kafka dependency

```groovy

implementation 'org.springframework.kafka:spring-kafka'
```




# Topic 생성

1. 카프카 bash 로 접속
```bash
docker exec -it kafka1 bash 
```


2. topic 생성
```bash
  kafka-topics --create \ 
  --bootstrap-server {broker-name}:{port} \ # 브로커중 하나에 접속 = 클러스터에 접속됨
  --replication-factor 2 \ # 파티션 내용을 복제할 브로커(follower) 수 지정(현재 1개) , 1로하면 leader 밖에 없음
  --partitions 3 \ # 파티션 수
  --topic chat-topic # 토픽 이름
```

설명 없는 버전
```bash
  kafka-topics --create \
  --bootstrap-server kafka2:9093 \
  --replication-factor 2 \
  --partitions 3 \
  --topic hello-topic

```


# 카프카에서 생성 or 버그 터져서 나가야 할때
```bash
exit
```