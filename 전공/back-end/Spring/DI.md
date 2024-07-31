##### DI(Dependency Injection) 이란?
의존성 주입 또는 의존관계 주입이라고 불림.

DI 는 외부에서 객체간의 관계(의존성)를 결정해 줌 = 객체를 직접생성하는 것이 아닌 외부에서 생성 후 주입시켜 주는 방식 

##### DI 원리
>우리가 클래스를 어노테이션(ex: @Service, @Component,@Repository) 으로  bean 으로 등록해주면 spring 에선 그 객체를 IoC 컨테이너로 가져와 저장한다. 
그러면 IoC 컨테이너에서 빈들의 의존성을 주입해준다.



