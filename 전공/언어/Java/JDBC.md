
**JDBC란?** 
java 에서 DB 로 쿼리 날리기 위해 존재하는 api

* JDBC api : 자바 표준 jdbc 인터페이스, 각 DB 에 맞춰서 구현체인 JDBC 드라이버를 사용하면 된다.

# JDBC 흐름

![[Pasted image 20250227221923.png]]

1. DriverManager 에 의해 JDBC 드라이버가 로드됨.
2. Connection : 트랜젝션 관리, statment 객체 생성등의 기능을 가진 Connection 객체가 생성됨.
3. Statment : Connection 객체에 의해 생성됨. DB 쿼리를 날리는 기능을 가지고 있다.
4. ResultSet : Statment 가 날린 쿼리의 반환값을 받아 저장한다.
5. 닫을때는 역순으로 닫힌다.



DB 와 소통할때만 커넥션객체를 사용하고 끝나면 커넥션객체를 삭제해야 한다(엄연히 객체기 때문에 메모리가 많이 든다.)
DB 에 쿼리를 보낼때마다 Connection 객체를 생성하는건 비용이 많이 든다.

그래서 커넥션 풀이라는것을 사용하여 커넥션 객체를 생성 삭제하는것이 아닌 Pool 에 저장해 놨다가 필요할때만 꺼내 쓰는 **커넥션 풀(Connection Pool)** 이라는 것을 사용한다.

![[Pasted image 20250227223601.png]]

* 커넥션 객체에 DB 와의 연결(Conection)을 미리 만들어 두고 Pool 에서 저장하고 있다가 어플리케이션에서 필요할때만 꺼내서 제공하고 사용이 끝나면 반환한다.
* 커넥션이 유효하지 않거나 쓰이지 않으면 닫고 새로운 커넥션을 추가한다.