클래스의 모든 필드 값을 파라미터로 받는 생성자를 자동으로 생성한다. 이 어노테이션을 사용하면 클래스의 모든 필드를 한번에 초기화 할수 있다.

``` java
@AllArgsConstructor
public class Person {
private String name;
private int age; // geeters and setters
}
```

AllArgsConstructor 를 사용하지 않으면 이렇게 변한다.

```java
public class Person {
private String name;
private int age;

public Person(String name, int age){
	this.name = name;
	this.age = age;
}
}
```