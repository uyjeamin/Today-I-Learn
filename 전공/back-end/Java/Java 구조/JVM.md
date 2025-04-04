Java virtual Machine 의 약자로 자바 가상 머신 이라는 의미를 가지고 있다.

#### 자바를 실행하기 위한 가상의 컴퓨터 라고 볼수 있다.
![[Pasted image 20240714174421.png]]
실행과정

1. java 어플리케이션을 실행하면 JVM 은 OS 로 부터 메모리를 할당함.
2. 자바 컴파일러 (javac) 가 자바 소스코드 (Xxx.java) 를 바이트코드(Xxx.class) 로 컴파일 한다. 

> [!note] .java 파일은 public 이 붙은 자바의 class  ex : ``public class example{}``  당 1개가 생성된다 Xxx.java 파일 내용에 따라 생성되는 Xxx.class 파일의 개수가 달라진다, 


예를 내부에 클래스를 하나더 정의하거나 익명클래스(람다식)을 사용할 경우 Xxx.class 파일이

```
LambdaExample.class
LambdaExample$$Lambda$1.class
```
이런식으로 생성된다 



3.  클래스 로더 (Class Loader) 를 통해 Xxx.class 가 각각 개별적인 Bytecode 파일이지만 이것을을 논리적으로 연결해 실행가능하게 한다. 그리고 JVM 메모리 (Runtime Data Areas) 로 로딩한다.
4. 로드된 클래스 파일 (Xxx.class) 들은 기계가 읽을 수 없으므로 실행 엔진 (Execution Engine) 을 통해 기계어로 변환하여 실행한다.
5. 이 과정에서 실행엔진에 의해 가비지 콜렉터 ([[Garbage Collector]]) 등도 작동한다.

자바 어플리케이션이 실행되면 JVM은 시스템으로 부터 필요한 메모리를 할당받고, 용도에따라 여러 영역으로 나누어 관리함. Method Area, Call Stack, Heap 이 대표적이다.

##### 메소드 영역 (Method Area)
>JVM 이 클래스 파일 (\*.class)  을 읽어 데이터를 저장하는 영역이다.
>이때 클래스 변수도 함께 생성된다.


##### 힙 (Heap)
>인스턴스가 생선되는 공간이다. 프로그램 실행 중 new 연산으로 생성되는 인스턴스 변수가 생성된다.

##### 호출 스택(Call Stack)
>메소드의 작업에 필요한 메모리 공간을 제공함. 메소드가 호출되면 호출 스택에 메모리가 할당되고 해당 메소드가 수행되는 동안 필요한 값 등을 저장하는데 사용된다. 메소드 실행이 완료되면 할당되었던 메모리 공간은 회수 된다.
