static 이란? 
java 에서 객체로 접근이 아닌 클래스레벨에서 접근() . 점으로접근할수 있도록 하는것 
#### 함수에서 static 예시
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


변수에서 static 예시
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