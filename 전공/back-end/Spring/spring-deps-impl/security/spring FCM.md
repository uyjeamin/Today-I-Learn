
사용 플로우


1. firebase 프로젝트 생성후, firebaseKey.json 을 안드/ios and backend 프로젝트 안에 둘다 넣어놓기
2. fireBase 의존성 추가.
3. firebaseConfig 클래스에서 FirebaseOptions 객체에 fireBaseKey 적제
4. 앱쪽에서 회원가입시, 해당 기기의 디바이스토큰을 fcm 한테 발급받음.
5. 발급받은 디바이스 토큰을 백엔드 api 로 보내서 해당 유저id 를 식별키로 하여 DB 에 등록
6. 해당 유저에게 알림을 보내고 싶으면 id 로 디바이스 토큰을 찾은 다음, firebaseMessaging 객체를 이용하여 fcm 에게 해당 디바이스 토큰과 보내고 싶은 메세지를 보냄.(이거 말고도 토픽으로 보내기등 다양한 방법이 있음)




의존성
```
implementation("com.google.firebase:firebase-admin:")
```




