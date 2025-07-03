

# Kafka 란?

pub-sub 모델의 메세지큐 기반, 분산 메시징 시스템이다.

![[Pasted image 20250223155915.png]]

구성요소
* event: 카프카에서 메세지를 보내는 단위, 메세지
* producer: 메세지를 보내는 주체
* partition: topic 을 물리적으로 나눈형태, queue처럼 선입선출로 메세지를 처리하지만 메세지를 일정기간 저장한다. 각각 분산되서 broker 에 넣어진다.
* consumer: 구독한 topic 의 partition 에게서 메세지를 받아와서 처리하는 어플리케이션.

![[Pasted image 20250223160736.png]]

* broker: 카프카를 실행하는 서버 인스턴스 zookeeper 에 의해 관리된다.
* zookeeper: broker 인스턴스를 관리한다
* Consumer Group → 여러 Consumer가 하나의 Topic을 병렬로 처리할 수 있도록 묶는 개념.


장점
* 대규모 트래픽처리, 분산처리에 효과적
* 클러스터 구성, fail-over, replication 같은 기능이 있다.
* 100kb/sec 의 속도 (다른 메세지 큐보다 빠르다)
* 특정 주기동안 디스크에 메세지를 저장하여 데이터의 영속성이 보장됨. Consumer 장애시 재처리 가능.

단점
* 범용 메세징 시스템에서 제공되는 다양한 기능이 제공되지 않음.