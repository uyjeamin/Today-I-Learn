static(정적) 은 고정된이라는 의미를 가지고 있다. 

* **클래스** 변수를 만들때 사용된다.
* 한 클래스에서 공통적인 값을 유지해야 할때 사용한다.
* 클래스가 메모리에 로딩될 때 생성되어 프로그램이 종료될 때 까지 유지된다.

기능 : 객체를 생성하지 않고도 다른 클래스에서 '클래스이름.변수명' 으로 호출이 가능하다.

static 를 사용해 정적 변수와 정적 메소드를 만들 수 있다. 이렇게 만들어진 정적 변수나 정적 메소드는 프로그램이 종료되기 전까지 사용할 수 있고, [[GC]] 에 의해 수집되지 않는다.


### 함수에서 static 예시
```java
class MathUtils {
    // static 메서드
    public static int square(int x) {
        return x * x;
    }
}

public class Main {
    public static void main(String[] args) {
        // MathUtils 클래스 이름으로 static 메서드 호출
        int result = MathUtils.square(5);
        System.out.println(result); // 출력: 25
    }
}

```
이런식으로 원래 객체를 생성하여 접근해야 하지만, ``클래스이름.함수이름 `` 형식으로 접근할수 있다.  이렇게 할 수 있는 이유는 프로그램 실행시 클래스 로더에 의해 **메모리** 에 올라가서 그렇다. 


### 변수에서 static 예시
```java
class Counter {
    // static 필드: 클래스 전체에서 공유
    public static int count = 0;

    // 일반 필드: 각 객체마다 별도로 존재
    public int instanceValue = 0;

    public void increment() {
        count++; // static 필드 증가
        instanceValue++; // 인스턴스 필드 증가
    }
}

public class Main {
    public static void main(String[] args) {
        Counter c1 = new Counter();
        Counter c2 = new Counter();

        c1.increment();
        c2.increment();

        // static 필드는 모든 객체에서 공유됨
        System.out.println("Static count: " + Counter.count); // 출력: 2

        // 일반 필드는 각 객체마다 별도임
        System.out.println("Instance value (c1): " + c1.instanceValue); // 출력: 1
        System.out.println("Instance value (c2): " + c2.instanceValue); // 출력: 1
    }
}

```
static 변수인 count 는 클래스 레벨에서 관리됨 -> 모든 객체가 같은 값을 공유함으로 
c1과 c2에서 각각 increment() 를 했을시 -> static 변수인 count 는 2 가 되고 일반 변수인 instanceValue 는 각각 인스턴스에서 1이 됨.