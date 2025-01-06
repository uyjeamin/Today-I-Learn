
>어떤 클래스를 변경 해야 하는 이유는 오직 하나뿐 이어야 한다.
>-로버트 C 마틴

#### 단일 책임 원칙이란 

 **하나의 객체는 반드시 하나의 기능만을 수행한다** 이다. 이는 
"클래스를 변경 해야 하는 이유는 오직 하나뿐 이어야 한다 와" 같은말이다.

 
 객체가 많은 기능과 동작등을 가지고 있으면 객체의 변경에 따른 영향력과 양과 범위가 매우 커지게 됨. 

**하지만 SRP 를 도입하면 객체의 동작 범위가 명확해져서 이러한 문제를 해결할 수 있다. -> 코드 의존성과 결합도를 줄일 수 있다.


예를 들어
**다음 코드는 단일 책임 원칙(SRP)을 지키지 않은 코드다**
****

```java
public class Animal {

    private String animal;

    public void setAnimal(String animal) {
        this.animal = animal;
    }

    public void cry () {

        if(animal == "Dog") { // 강아지
            System.out.println("bark!");
        }
        else if(animal == "Cat") { // 고양이
            System.out.println("meow..");
        }
    }

}
```

Animal 이라는 객체에서 구현하고 있는 울음소리 메서드(cry) 에서 강아지일때 와 고양이일때의 울음소리를 **둘다 구현하고 있기 때문이다** 

**단일 책임 원칙(SRP)을 지킨 코드**

```java
abstract class Animal {
    abstract void cry();
}

class Dog extends Animal {
    @Override
    void cry() {
        System.out.println("bark!!!");
    }
}

class Cat extends Animal {
    @Override
    void cry() {
        System.out.println("Meow...");
    }
}
```

이러한 문제를해결하기 위해 Animal 클래스를 상속 받는 두개의 클래스를 만들어 각각 하나의 기능을 하나식만 만들어 사용하고 있다. 
-> **하나의 클래스에 하나의 기능을 가지고 있다.**