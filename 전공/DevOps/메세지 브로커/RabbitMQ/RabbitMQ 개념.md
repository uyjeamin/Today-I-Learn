

# RabbitMQ 란?
* AMQP 프로토콜을 구현한 대표적인 메세지 브로커이다.

![[Pasted image 20250223154212.png]]

동작 방식
1. producer: 메세지를 보낸다
2. exhange: 받은 메세지를 목적에 맞는 queue 에 보낸다. (routingKey 가 일치하는 queue 에 보내거나, 등록된 모든 queue 에 보내는등 여러가지 모드가 있다.)
3. queue: 메세지를 쌓아둔다
4. consumer: 자신과 연결된 queue 의 메세지를 받고 활용한다. (연결되지 않은 queue 의 메세지는 받지 못함.)

* 장점
	* 브로커 중심적인 형태로 publisher 의 메세지가 consumer 에게 가는걸 보장하는 것에 초첨을 맞춤.
	* 확장성 뛰어남, 복잡한 라우팅 지원
	* 20kb/sec 속도
	* 관리적인 부분이나 기능구현의 위한 서비스 구축시 사용
* 단점
	* MQ 서버가 queue의 데이터가 다 날라감.
	* producer 와 consumer 의 결합도 높음.

