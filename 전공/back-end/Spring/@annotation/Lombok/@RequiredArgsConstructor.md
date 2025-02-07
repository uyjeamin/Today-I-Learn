초기화되지 않은 final 필드나, @NonNull 이 붙은 필드에 대해 생성자를 생성해 줍니다.

새로운 필드를 추가할 때 다시 생성자를 만들어서 관리해야하는 번거로움을 없애준다. (@Autowired를 사용하지 않고 의존성 주입)

```java
@RestController
@RequiredArgsConstructor
@RequestMapping("/example")
public class RequiredArgsConstructorControllerExample {

  private final FirstService firstService;
  private final SecondService secondService;
  private final ThirdService thirdService;
  
  ...
}
```

해당 필드로 구성된 생성자를 @RequiredArgsConstructor가 자동으로 생성자 주입에 대한 코드를 생성.




