
사용 플로우


1. firebase 프로젝트 생성후, 해당
2. 앱쪽에서 회원가입시, 해당 기기의 디바이스토큰을 fcm 한테 발급받음.
3. 발급받은 디바이스 토큰을 백엔드 api 로 보내서 해당 유저id 를 식별키로 하여 DB 에 등록
4. 해당 유저에게 알림을 보내고 싶으면 id 로 디바이스 토큰을 찾은 다음, firebaseMessaging 클래스의 객체를 이용하여 fcm 에게 해당 디바이스 토큰과 보내고 싶은 메세지를 보냄,




의존성
```
implementation(Dependencies.FLYWAY:"org.flywaydb:flyway-mysql:")
```




