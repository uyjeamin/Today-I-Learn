

도커 컨테이너로 RabbitMq 설치후 띄우기
```cmd
docker run -it --rm --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:3.13-management
```


기본 guest 계정은 도커 외부 접속이 안되고, 제약도 많으니 새 계정 생성
```
docker exec -it rabbitmq rabbitmqctl add_user myuser mypass
>> docker exec -it rabbitmq rabbitmqctl set_user_tags myuser administrator
>> docker exec -it rabbitmq rabbitmqctl set_permissions -p / myuser ".*" ".*" ".*"
```

