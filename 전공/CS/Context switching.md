컨텍스트 스위칭은 프로세스나 스레드가 자신의 작업을 다 끝내지 못하고, 인터럽트 요청에 의해 다음 우선순위의 프로세스 실행 되어야 할때 가자신이 여태까지 한 작업으로 인한 상태(Context) 를 프로세스는 PCB(Process Control Block), 쓰레드는 TCB(Thread Control Block) 에 저장한 후 다음에 실행할 프로세스나 스레드의 Context 를 PCB 에서 꺼내와 적재는 것이다.





Context Switching 때 CPU 는 아무것도 하지 못한다. -> 컨텍스트 스위칭이 많아지면 CPU 효율이 떨어짐.