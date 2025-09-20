
@Bean 어노테이션은 @Configuration 이 붙은 클래스에서 해당 클래스 타입의 빈을 만드는 어노테이션이다.

```java
@Configuration
class AppConfig {
    @Bean
    fun myService(): MyService = MyService()
}

@Service
class UserService(
    private val myService: MyService  // 이름이 달라도 타입이 같으니까 자동 주입됨
)

```

보통 라이브러리같이 Component 를 붙이지 못하거나 설정파일을 한곳에서 설정한후 빈등록해서 주입하기 위해 사용됨. 여기서 빈등록하는 메서드 이름이 Bean 의 이름이 된다.

같은 클래스타입의 빈이 자기밖에 없을때는 그 클래스 타입의 객체로 자동 주입되지만,

같은 클래스타입의 빈이 여러개일때는 어떤 이름의 빈을 쓸지 지정해야 한다. 
-> @Qualifier("beanName") beanName 이라는 메서드에서 태어난 빈을 사용하겠다는 뜻(메서드 이름 == 빈 이름)

아니면 어떤 메서드의 빈을 우선적으로 쓸건지 정할 수 있다.
-> @Primary 을 붙인 메서드의 빈을 우선적으로 씀 