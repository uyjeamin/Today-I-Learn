
```
HHH000099: an assertion failure occurred (this may indicate a bug in Hibernate, but is more likely due to unsafe use of the session): org.hibernate.AssertionFailure: null identifier
```

이 오류는 기본키에 null 이 들어갔다는 오류인데 ``@mapsId`` 를 쓸때 ``@Id`` 가 붙어있는 필드에 직접적으로 값을 또 넣어주면 하이버네이트가 이러한 오류를 이르킨다.