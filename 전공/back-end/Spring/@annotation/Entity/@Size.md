컬렉션, 배열, 문자열 등의 크기를 제한하는데 사용됨.

```java
import javax.validation.constraints.Size;

public class User {
    @Size(min = 2, max = 30) // 2글자 이상, 30 글자 미만
    private String name;

    @Size(min = 1) // 역활이 1개 이상
    private List<String> roles; 

    @Size(min = 5, max = 10) // 배열크기 5 이상 10 이
    private int[] numbers;

    // getters and setters
}
```