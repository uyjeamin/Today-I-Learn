JWT(Json Web Token) 이란?
**Json 포맷을 이용하여 사용자에 대한 속성을 저장하는 Claim 기반 web Token** 이다. 
**토큰 자체를 정보로 사용하는 Self-Contained 방식으로 정보를 안전하게 처리**

## 개념

* JWT(Json Web Token)란 Json 형식을 이용하여 사용자에 대한 속성을 Claim 기반의 Web Token 으로 저장하는 것이다.

* JWT는 토큰 자체를 정보로 사용하는 Self-Contained 방식으로 정보를 안전하게 전달한다. 

* 주로 회원 인증이나 정보 전달에 사용된다.
![[Pasted image 20240802202519.png]]
**로컬 스토리지(local Storage)란?** 
>==JWT Token 이 저장되는 위치중 하나==로 데이터를 브라우저에 반영구적으로 저장됨 브라우저를 종류 후 재시작해도 데이터가 남아있음. 다른 창과 브라우저를 통해서도 접근이 가능함.
>JS 를 통해 (local storage)에 데이터 저장 가능. 

애플리케이션이 실행될 때, JWT를 static 변수와 로컬 스토리지에 저장하게 됨. 

