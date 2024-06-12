hash map 이란?

: map 인터페이스를 구현한 hash table이다.

key 와 value 값으로 값을 저장하는 구조 이다.

- (key , value) 쌍으로 entry 라고 부른다.

-특징

1. 키와 벨류 값에 null 을 허용한다.
2. 요소의 삽입 삭제시간이 매우 빠르다.
3. 요소의 검색시간은 더 빠르다.
4. 인덱스 요소를 이용하여 요소에 접근할수 없다 오직 ‘키’ 로민 검색해야 한다.

- 해시맵 생성

```java
HashMap<String, String> h1 = new HashMap<String, String>();
// java 10부터 var h = new HashMap<String, String>();로 간략히 쓸 수 있음.
```

HashMap<K, V> Key 와 Value 로 사용될 타입을 지정하여 생성함.

- 해시맵에 요소 삽입

```java
h1.put("1","one");
h1.put("2","two");
```

- 키와 매칭되는 값 읽기

```java
h1.get("1"); // one
```

- ‘키’ 로 요소 삭제하기

```java
h1.remove("2"); // two 가 삭제됨
```