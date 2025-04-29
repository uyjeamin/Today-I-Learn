정확한 Class type 을 알지 못하고, 이름만 알더라도 그 클래스의 method, type, variable들에 접근할 수 있게 하는 자바 api
**Runtime 에 동적으로 특정 Class 의 정보를 추출할 수 있는 프로그래밍 기법**이다.


# Car 클래스
```java
public class Car {  
  
    public int speed;  
  
    public String name;  
  
    private Car() {  
    }  
    private Car(int speed, String name) {  
        this.speed = speed;  
        this.name = name;  
    }  
  
    private void run(int km){  
        System.out.println(km+"km 를 이동함");  
    }  
  
    private void run(){  
        System.out.println("달리고 있음");  
    }  
}
```




# Main 클래스
```java
Class carClass = Class.forName("Car");  
  
//인자가 없는 기본생성자.  
//Constructor constructor = carClass.getDeclaredConstructor()  
  
// 인자가 있는 생성자  
Constructor constructor = carClass.getDeclaredConstructor(int.class, String.class);  
constructor.setAccessible(true);  
Car car = (Car) constructor.newInstance(10, "페라리");  
  
//car 객체에서 speed 라는 이름을 가진 필드를 가져옴  
Field field = car.getClass().getField("speed");  
  
System.out.println("Before Change: speed = " + field.get(car));  
  
//접근제한자를 무시하고 접근할 수 있게 해줌 private 도 접근 가능  
field.setAccessible(true);  
  
//car 객체의 speed 필드에 45 의 값을 넣음  
field.set(car, 45);  
System.out.println("After Change: speed = " + field.get(car));  
  
// car 클래스에서 int 매게변수를 1개 가지고 있는 run 메서드를 가져옴  
Method method = car.getClass().getDeclaredMethod("run", int.class);  
  
  
method.setAccessible(true);  
  
// 인자를 100 으로 하고 메서드 호출  
method.invoke(car, 100);  
  
// 이 클래스가 구현하고 있는 모든 인터페이스 가져옴  
//Class[] interfaces = car.getInterfaces();
```


결과 
```
Before Change: speed = 10
After Change: speed = 45
100km 를 이동함
```


이렇게 