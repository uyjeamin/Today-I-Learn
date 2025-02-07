컨트롤러가 아닌 다른 계층에서 유효성 검사를 하고 싶을 때
@Vaildated를 사용함
AOP 기반으로 메소드의 요청을 가로채서 **유효성검사**를 진행해준다.
spring 프레임워크에서 제공하는 어노테이션

*\유효성 검사란?
>어떤 데이터 (주로 사용자 또는 다른 서브의 request 가 유효한지, 잘못된 내용이 있는지 확인하는 단계)

``` java
@Service
@Validated
public class UserService {
    public void createUser(@Valid SignUpReq signUpReq) {
    }
}
```

@Vaildated를 서비스 클래스 위에 선언하고 유효성 검사를 하고 싶은 클래스에 @Valid를 붙여주면 된다.

다른 기능으로는 유효성 검증 그룹을 지정해 줄 수 있다.
동일한 클래스에 대한 제약 조건이 다른 경우 제약조건이 적용될 검증 그룹을 지정할수 있다.
하는법 내용이 없는 마커 인터페이스를 간단히 정의해 주면 됨 그리고
제약 조건 어노테이션(@NotNull, Email 등)의 괄호 안에 
"groups=클래스명"을 지정해 주면 된다. 그룹 지정 기능은 코드가 복잡해 거의 사용 안함 참고만

@Validated 예외

유효성 검사에 실패하면 @Valid만 사용하던 것과는 달리
ContraintViolationException 예외가 발생함
클래스에 유효성 검사를 위한 AOP 어드바이스 또는 인터셉터가 등록된다.

