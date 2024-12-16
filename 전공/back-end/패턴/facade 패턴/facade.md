### facade 란?
3줄 정리
* 클라이언트가 내부 클래스의 세부사항에 관련 없이 facade 만을 통해서 접근 가능하다
* 클라이언트가 내부상황을 몰라도 facade 를 통해 작동가능하게 할 수 있다.(캡슐화한다)
* 각 하위 시스템은 독립적으로 작동하며, facade 가 이것을 호출해 통합한다.


단점 
* facade 클래스를 또하나 작성해야함.
* 너무 많은 기능을 facade 클래스에 넣으면 God Object 가 될 수도 있음.

ex) 스마트폰 카메라

// 하위 시스템
```
class CPU {
    void start() { System.out.println("CPU Started"); }
    void execute() { System.out.println("CPU Executing Instructions"); }
}

class Memory {
    void load() { System.out.println("Memory Loaded"); }
}

class HardDrive {
    void read() { System.out.println("Hard Drive Reading Data"); }
}

```

// Facade

```
 class ComputerFacade {
    private CPU cpu;
    private Memory memory;
    private HardDrive hardDrive;
    
    public ComputerFacade() {
        cpu = new CPU();
        memory = new Memory();
        hardDrive = new HardDrive();
    }

    public void startComputer() {
        System.out.println("Starting computer...");
        cpu.start();
        memory.load();
        hardDrive.read();
        cpu.execute();
        System.out.println("Computer started successfully.");
    }
}
```


   

```

// 클라이언트
public class Main {
    public static void main(String[] args) {
        ComputerFacade computer = new ComputerFacade();
        computer.startComputer();
    }
}
```

결과값
```
Starting computer...
CPU Started
Memory Loaded
Hard Drive Reading Data
CPU Executing Instructions
Computer started successfully.
```