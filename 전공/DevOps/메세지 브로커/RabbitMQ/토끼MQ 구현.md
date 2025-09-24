

도커 컨테이너로 RabbitMq 설치후 띄우기
```cmd
docker run -it --rm --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:3.13-management
```



커스텀 properties 쓸거면 이런 spring: 하위에 두지 말고, rabbitmq: 가 최상위 도메인이 되도록 만들자. (spring 아래에 두면 그 값을 기준으로 자동으로 rabbitmq 의존성에서  rabbitmqTemplate 을 생성한다.)
```
rabbitmq:  
  port: ${RABBITMQ_PORT:5672}  
  host: ${RABBITMQ_HOST:localhost}  
  username: ${RABBITMQ_USERNAME:guest}  
  password: ${RABBITMQ_PASSWORD:guest}
```