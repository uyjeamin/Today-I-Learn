
**먼저 Spring Security 란?**
>Spring Security는 애플리케이션에 보안 기능을 추가하기 위해 만들어진 프레임워크임.

#### Spring Security Filter Chain
>Spring Security 에서 제공하는 인증,인가를 위한 필터들의 모음이다

여러 보안 필터들의 체인(순서에 따라 연결된 필터들)으로 구성되어 있다. 각각의 필터는 HTTP 요청을 가로챔 

#### SpringSecurityFilterChain 구조
![[스크린샷 2024-08-26 193806.png]]

Filter 구조는 위와 같다. 
FilterChain과 
SecurityFilterChain으로 나뉘어진다.

 ***FilterChain** 은 was(톰켓)의 필터 체인 이다. 그리고 **SecurtityFilterChain** 은 스프링시큐리티에서 사용하는 필터 체인이다.

* ***DelegationgFilterProxy란?**
>기존 필터체인과 분리된 필터체인에게 HTTP 요청을 가져와야 하기 때문에 생겨난 필터이다.

DelegationgFilterProxy 를 FilterChain에 추가하여 **SecurityFilterChain** 과 연결시주는 역활을 함.

##### **Security Filter Chain**
* 실제로 요청을 처리하는 여러 개의 보안 필터들이 들어있는 체인이다. 
##### 작동방식.
*  요청이 들어오면 필터에 순서대로 요청을 처리한다. 
* 각각의 보안필터들은 독립적이지만 체인으로 묶여있어 순서대로 실행됨.
* 요청을 검사하고 다음필터로 요청을 전달할지, 아니면 응답을 반환할지 결정함.
* 모든 필터를 걸치면 요청은 실제 컨트롤러로 전달됨.
* 응답도 Filter Chain(SpringSecurityFilterChain 도)을 거쳐 클라로 전달됨.

##### 들어있는 필터들
* UsernamePasswordAuthenticationFilter: 기본적인 로그인 처리(id,pw 이용한 인증)
* BasicAuthenticationFilter: HTTP 기본 인증 처리.
* BearerTokenAuthoenticationFilter:JWT와 같은 토큰기반 인증 처리.
* SecurityContextPersistenceFilter: SecurityContext(현재 인증된 사용자의 정보를 관리함.) 를 저장,복원함.
- **CsrfFilter**: CSRF(Cross-Site Request Forgery : 사용자가 로그인된 상태를 악용하여 공격) 공격 방지.
- ExceptionTranslationFilter: 인증 및 권한 부여 과정에서 발생한 예외를 처리.
- FilterSecurityInterceptor: 권한 검사를 수행하여 접근을 제어



