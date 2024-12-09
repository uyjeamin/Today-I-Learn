


스프링 트로젝트 루트모듈에 Dockerfile 생성(위치는 상관없음)

```Dockerfile
# 기본 OpenJDK 이미지 사용  
FROM openjdk:17-jdk  
  
# .jar 파일의 경로를 빌드 컨텍스트에 매핑  
COPY . /app  
  
# JAR 파일을 복사하고 어플리케이션 실행을 위한 entry point 지정  
ENTRYPOINT ["java", "-Dspring.profiles.active=doker", "-jar", "/app/simpleCRUD-0.0.1-SNAPSHOT.jar"]

```
작성


### 도커 이미지 빌드

```
```
cd build/libs
docker build -t {도커 허브 ID}/{레포지토리 이름:버전} {Dockerfile 경로}
```

ex :
```

``` prompt
docker build -t 
jeamin08/myrepo:1.0.0 -f C:\Users\user\Desktop\git\VerySimpleCRUD\simpleCRUD\Dockerfile .

```
