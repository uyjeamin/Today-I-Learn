
REST 란?
간단히 말하자면,
URI 를 통해 자원을 명시하고, POST, GET, PUT ,DELETE)와 같은 HTTP 메서드를 통해 해당 자원에 CRUD Operation(CRUD 작업)을 할 수있게 한 것이다.

주로 json 이나 XML 로 데이터를 주고받는다.

### REST API
* 이런 REST 를 기반으로 서비스 API 를 구현한것이 **REST API**이다.

#### REST API 설계 기본 규칙

리소스란? : urI 를 통해 접근할 수 있는 특정 데이터나 객체를 의미함.

참고 리소스 원형  

* 도큐먼트 : 객체 인스턴스나 데이터베이스 레코드와 유사한 개념.
* 컬렉션 : 서버에서 관리하는 디렉터리라는 리소스
* 스토어 : 클라이언트에서 관리하는 리소스 저장소


1. URI는 정보의 자원을 표현해야 한다.
- resource는 동사보다는 명사를, 대문자보다는 소문자를 사용한다.
- resource의 도큐먼트 이름으로는 단수 명사를 사용해야 한다.
- resource의 컬렉션 이름으로는 복수 명사를 사용해야 한다.
- resource의 스토어 이름으로는 복수 명사를 사용해야 한다.  
    ex) GET /Member/1 -> GET /members/1

2. 자원에 대한 행위는 HTTP Method(GET, PUT, POST, DELETE 등)로 표현한다.
- URI에 HTTP Method가 들어가면 안된다.  
 ex) GET /members/delete/1 -> DELETE /members/1

- URI에 행위에 대한 동사 표현이 들어가면 안된다.(즉, CRUD 기능을 나타내는 것은 URI에 사용하지 않는다.)  
 ex) GET /members/show/1 -> GET /members/1  
 ex) GET /members/insert/2 -> POST /members/2
 
- 경로 부분 중 변하는 부분은 유일한 값으로 대체한다.(즉, :id는 하나의 특정 resource를 나타내는 고유값이다.)  
ex) student를 생성하는 route: POST /students  
ex) id=12인 student를 삭제하는 route: DELETE /students/12

- 슬래시 구분자(/ )는 계층 관계를 나타내는데 사용한다.  
ex) [http://restapi.example.com/houses/apartments](http://restapi.example.com/houses/apartments)
- URI 마지막 문자로 슬래시(/ )를 포함하지 않는다.
 ex) [http://restapi.example.com/houses/apartments/](http://restapi.example.com/houses/apartments/) (X)

- 하이픈(- )은 URI 가독성을 높이는데 사용
    - 불가피하게 긴 URI경로를 사용하게 된다면 하이픈을 사용해 가독성을 높인다.
    -> 밑줄(_ )은 URI에 사용하지 않는다.

- URI 경로에는 소문자가 적합하다.
    
    - URI 경로에 대문자 사용은 피하도록 한다.
    - RFC 3986(URI 문법 형식)은 URI 스키마와 호스트를 제외하고는 대소문자를 구별하도록 규정하기 때문
    
- 파일확장자는 URI에 포함하지 않는다.
    
    - REST API에서는 메시지 바디 내용의 포맷을 나타내기 위한 파일 확장자를 URI 안에 포함시키지 않는다.
    - Accept header를 사용한다.  
        ex) [http://restapi.example.com/members/soccer/345/photo.jpg](http://restapi.example.com/members/soccer/345/photo.jpg) (X)  
        ex) GET / members/soccer/345/photo HTTP/1.1 Host: restapi.example.com Accept: image/jpg (O)
    
- 리소스 간에는 연관 관계가 있는 경우
    
    - /리소스명/리소스 ID/관계가 있는 다른 리소스명  
        ex) GET : /users/{userid}/devices (일반적으로 소유 ‘has’의 관계를 표현할 때)


##### 마지막으로 RESTful(Representational State Transger) 이란?

누군가가 여태까지 설명한 REST API 설계 규칙들을 잘 지키기 위한 방법으로 누군가가 공식적으로 발표한건 아님.

#### **즉, REST API 설계규칙을 잘 지키는 API 를 RESTful API 라고 한다.**  

