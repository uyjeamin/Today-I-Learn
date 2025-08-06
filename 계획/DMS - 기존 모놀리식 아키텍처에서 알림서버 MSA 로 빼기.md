


모놀리식 서버 Rabbit MQ 로 알림 발행 
-> 알림서버에서 알림 수신후, FCM 서버로 sendAsync() 
성공시 -> Rabbit MQ Queue 에서 메세지 삭제
실패시 -> 서킷 브래리커 작동 -> Dead Letter Queue 로 메세지 이동.