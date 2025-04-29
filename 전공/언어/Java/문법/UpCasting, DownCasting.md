
# UpCasting 은 자식타입이 부모타입으로 올려서 변환되는것

```java
class Animal { }
class Dog extends Animal { }

Dog dog = new Dog;
Animal animal = dog;
```

* 컴파일러가 알아서 변환해준다
* 완전 안전함
* dog(자식클래스) 에만 정의되어 있는 메서드등은 쓰지 못한다.(부모클래스타입이 되었으니까) 하지만 오버라이드 된 메서드는 그대로 쓸 수 있다.
## 용도
* 메서드 매게변수가 부모클래스로 되어있으면 자식클래스도 넣을 수 있음(자동으로 업캐스팅됨) -> 다형성과 코드 유연성이 목적

```java
List<Animal> animals = new ArrayList<>();
animals.add(new Dog()); // 업캐스팅
animals.add(new Cat()); // 업캐스팅
```


# DownCasting 부모타입이 자식타입으로 내려서 변환되는것

```java
class Animal { }
class Dog extends Animal { }

//업케스팅 먼저 함.
Animal animal = new Dog;

//다운 케스팅
Dog dog = (Dog) animal;
```

* 직접 명시적 형변환(Casting) 을 해주어야 한다.
* 부모 타입으로 업캐스팅 한것을 다시 자식 타입으로 되돌리기 위해서 사용하는 것이다.
* 런타임 환경에서 타입이 맞지 않으면 ClassCastException 이 터진다.