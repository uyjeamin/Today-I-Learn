```java
private fun byteStringToUuid(byteString: ByteString): UUID {  
    val byteBuffer: ByteBuffer = byteString.asReadOnlyByteBuffer()  
    return UUID(byteBuffer.long, byteBuffer.long)  
}
```
이 부분에서 어떻게 readOnly 가 작동되는지 보았더니

```java
abstract class ByteBuffer extends Buffer {
    final boolean isReadOnly;

    // 생성자
    ByteBuffer(int capacity, boolean isReadOnly) {
        super(capacity);
        this.isReadOnly = isReadOnly;
    }

    public final boolean isReadOnly() {
        return isReadOnly;
    }
}
```

이런식으로 내부에서 플래그 변수를 통해 put 메서드로 내부 byte 를 수정하려고 하면 예외를 띄우는 로직으로 작동하고 있었다. 단순 ByteBuffer 클래스 뿐만 아니라 다른 클래스들에서도 적용되고 있는 패턴인거 같다.