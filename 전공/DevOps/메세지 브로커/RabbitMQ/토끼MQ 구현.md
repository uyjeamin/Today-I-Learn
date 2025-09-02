

도커 컨테이너로 RabbitMq 설치후 띄우기
```cmd
docker run -it --rm --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:3.13-management
```
