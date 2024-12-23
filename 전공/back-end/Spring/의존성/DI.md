##### DI(Dependency Injection) 이란?
의존성 주입 또는 의존관계 주입이라고 불림.

DI 는 외부에서 객체간의 관계(의존성)를 결정해 줌 = 객체를 직접생성하는 것이 아닌 외부에서 생성 후 주입시켜 주는 방식 

##### DI 원리
>우리가 클래스를 어노테이션(ex: @Service, @Component,@Repository) 으로  bean 으로 등록해주면 spring 에선 그 객체를 IoC 컨테이너로 가져와 저장한다. 
그러면 IoC 컨테이너에서 빈들의 의존성을 주입해준다.

#### DI 의 장점
1. 의존성이 낮아짐 -> Unit Test가 용이해진다 
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
* spring 에서 가장 권장하는 DI 방법.

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

## 2.Setter 주입
Setter를 이용하여 의존 관계를 주입하는 방법이다. `주입받는 객체가 변할 수 있는 경우`에 사용한다.

```java
@Service 
public class UserServiceImpl implements UserService { 
    private UserRepository userRepository; 
    private MemberService memberService; 
    @Autowired 
    public void setUserRepository(UserRepository userRepository) { 
        this.userRepository = userRepository; 
    } 
    @Autowired 
    public void setMemberService(MemberService memberService) { 
        this.memberService = memberService; 
    } 
}

```

만일 `@Autowired` 로 주입할 대상이 없는 경우에는 오류가 발생한다. ⇒ 빈에 존재하지 않는 경우

주입할 대상이 없도록 하려면 `@Autowired(required = false)` 를 통해 설정이 가능하다.


## 3.필드 주입(Field Injection)
필드에 바로 의존 관계를 주입하는 방법이다.

```java
@Service 
public class UserServiceImpl implements UserService { 
    @Autowired
    private UserRepository userRepository; 
    @Autowired
    private MemberService memberService; 
}
```

특징
* 코드가 간결하다
* 외부에서 변경이 불가능하다.

필드주입은 DI 프레임워크(스프링같은) 가 존재해야지만 사용이 가능하다. -> 사용을 지양해야 한다.