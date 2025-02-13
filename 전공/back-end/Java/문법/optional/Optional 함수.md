
```java
Optional.of(T value)
```
**변수가 null 이면 nullPointerException 발생시킴**


```java
Optional.ofNullable(T value)
```
**변수가 null 이면 Optional.empty() 를 반환함**