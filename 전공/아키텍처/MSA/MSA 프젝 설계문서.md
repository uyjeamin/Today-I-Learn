

1. 클라 요청 

2. API gateway server 에서 jwt 인증/인가중 **인증** 을 처리 -> 인증되면 클라이언트가 호출한 api 에 header 에 JWT 에서 파싱한 role, userId 같은 인가에 필요한 정보를 넣어서 각각 MSA 서버에서 DB 조회 안해도 인가 되게 만듬