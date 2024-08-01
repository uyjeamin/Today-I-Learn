##### DI(Dependency Injection) 이란?
의존성 주입 또는 의존관계 주입이라고 불림.

DI 는 외부에서 객체간의 관계(의존성)를 결정해 줌 = 객체를 직접생성하는 것이 아닌 외부에서 생성 후 주입시켜 주는 방식 

##### DI 원리
>우리가 클래스를 어노테이션(ex: @Service, @Component,@Repository) 으로  bean 으로 등록해주면 spring 에선 그 객체를 IoC 컨테이너로 가져와 저장한다. 
그러면 IoC 컨테이너에서 빈들의 의존성을 주입해준다.

#### DI 의 장점
1. 의존성이 낮이짐 -> Unit Test가 용이해진다 
2. 하나의 객체에 의존하지 않음 -> 코드의 재활용성이 높아짐
3. 객체간의 의존성을 줄이거나 없앨 수 있다.
4. 객체 간의 결합도를 낮추면서 유연한 코드를 작성할 수 있다.


DI 를 실현하는 방법은 3가지가 있다.

## 1. 생성자 주입
```java
@Service 
public class UserServiceImpl implements UserService {   
    private UserRepository userRepository; 
    private MemberService memberService; 
    @Autowired 
    public UserServiceImpl(UserRepository userRepository, MemberService memberService) { 
        this.userRepository = userRepository; 
	this.memberService = memberService; 
    } 
}
```

* 생성자 주입은 객체 생성시 의존성을 강제하는 데 사용됨.
* 의존성이 한번 설정되고 이후에는 변경할 수 없음을 보장함.
* 실수로 객체를 수정하는걸 방지함.

스프링에서는 생성자가 1개만 있을 경우에는 `@Autowired` 는 생략 가능하다. 그래서 아래와 같이 변경이 가능하다.

```java
@Service 
public class UserServiceImpl implements UserService {   
    private UserRepository userRepository; 
    private MemberService memberService; 

    public UserServiceImpl(UserRepository userRepository, MemberService memberService) { 
        this.userRepository = userRepository; 
	this.memberService = memberService; 
    } 
}
```
