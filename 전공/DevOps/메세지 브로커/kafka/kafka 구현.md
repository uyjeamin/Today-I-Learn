

# Topic 생성

1. 카프카 bash 로 접속
```bash

docker exec -it kafka1 bash 
```


2. topic 생성
```bash
  kafka-topics --create \ 
  --bootstrap-server localhost:9092 \ # 브로커중 하나에 접속 = 클러스터에 접속됨
  --replication-factor 2 \ # 파티션 내용을 복제할 브로커(follower) 수 지정(현재 1개) , 1로하면 leader 밖에 없음
  --partitions 3 \ # 파티션 수
  --topic chat-topic # 토픽 이름
```