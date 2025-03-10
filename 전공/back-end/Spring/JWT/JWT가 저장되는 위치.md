토큰이 저장되는 위치는 대표적으로 **비공개 변수**, **로컬 스토리지**, **세션 스토리지** ,**쿠기** 로 분류할 수 있다. 모든토큰 방식(일반적인 토큰, 엑세스 토큰, 리프레시 토큰) 자체가 클라이언트에서 서버로 부터 받은 토큰을 저장해야 하기 때문에 어디에 저장할 건지에 대한 고민은 필수적이다.

* **비공개 변수** 는 브라우저가 새로고침될 때마다 유지가 되지 않기 때문에 사용자는 새로고침 할 때마다 재접속해야 함 -> 많이 안쓰임.

**로컬 스토리지(local Storage)란?** 
>==JWT Token 이 저장되는 위치중 하나==로 데이터를 브라우저에 반영구적으로 저장됨 브라우저를 종류 후 재시작해도 데이터가 남아있음. 다른 창과 브라우저를 통해서도 접근이 가능함.
>JS 를 통해 (local storage)에 데이터 저장 가능. 

**세션 스토리지(sessionStorage)**
세션 스토리지(session storage)는 로컬 스토리지와 유사한 기능을 가짐. 하지만 브라우저가 닫히면 데이터가 사라지고, 다른창과 브라우저로의 데이터 공유가 불가능하다.

웹 스토리지는 자바스크립트로 제어 가능 -> *XSS*공격에 대한 위험이 있음. 해커가 js 코드를 웹페이지에 심어 해당 코드로 사용자 정보탈취 가능

XSS 공격이란?
>크로스 사이트 스크립팅(Cross Site Scripting) 이라는 뜻으로 
>공격자가 상대방의 브라우저에 스크립트가 실행되도록 해 사용자의 세션을 가로채거나 웹사이트를 변조하거나, 악의적 콘텐츠 삽입, 피싱 공격을 하는것. 