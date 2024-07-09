이름 그대로 null 이 아닌 값을 가져야 할때 사용됨.

`null`이 아닌 값을 가져야 할 때 사용됩니다. 예를 들어, 사용자 객체에서 사용자의 이름이 반드시 필요하다면 이름 필드에 `@NotNull`을 적용할 수 있습니다.
```java
public class User { @NotNull private String name; // getters and setters }
```