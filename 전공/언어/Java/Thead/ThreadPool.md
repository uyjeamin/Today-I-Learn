ThreadPool 이란?

스프링서버는 요청이 1개 올때마다 한개의 쓰레드를 할당하여 사용함 -> 모든 요청이 올때마다 쓰레드를 생성하면 쓰레드가 엄청 많이 생성되는 문제가 생김 -> 쓰레드풀을 사용해서 미리 일정 개수의 쓰레드를 생성하여 관리함

**쓰레드풀은 미리 일정 개수의 쓰레드를 생성하여 관리하는 기법을 쓰레드 풀이라고 한다.**

설정에 따라 달라지겠지만, 기본적으로 일정개수의 쓰레드가 생성되어 요청이 올때마다 was 에서 꺼내와 요청에 할당한다. 만약 기본적으로 주어진 쓰레드의 개수를 넘어서는 요청이 들어오면 추가적으로 쓰레드를 생성한다. 최대로 생성할 수 있는 쓰레드의 수는 제한되어 있고, 사용되지 않는 추가 쓰레드는 삭제되게 된다.



