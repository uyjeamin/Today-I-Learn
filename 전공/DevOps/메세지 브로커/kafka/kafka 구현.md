

```bash

docker exec -it kafka1 \ 
  kafka-topics --create \ #topic 생성
  --bootstrap-server localhost:9092 \ # 브로커중 하나 9092 포트인 브로커에 접속 
  --replication-factor 2 \
  --partitions 3 \
  --topic chat-topic
```
