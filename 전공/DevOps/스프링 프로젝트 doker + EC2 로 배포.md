
참고 : https://nervous-actress-8c0.notion.site/1092a40839454484860f5a3cd7e866ae?pvs=73




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

#### docker login하기
```Terminal
docker login
```
그러면 
``` Terminal
Your one-time device confirmation code is: SRNH-QVST
Press ENTER to open your browser or submit your device code here: https://login.docker.com/activate

Waiting for authentication in the browser…

```
이런 형식으로 뜰텐데 이때 링크를 클릭하고 
one-time 인증코드를 적는다 ``SRNH-QVST


### 로컬에 도커 이미지 빌드
```Terminal
cd Dockerfile 경로
docker build -t jeamin08(도커 허브 ID)/myrepo:1.0.0(레포지토리 이름:버전) .{Dockerfile 경로}
```



### 생성한 이미지 DockerHub에 push
자신의 repo에 push 합니다.
```Terminal
docker push jeamin08/myrepo:1.0.0(version)
```

### 보안그룹 설정
![[Pasted image 20241212102316.png]]



docker 명령어에서 sudo 안붙여도 되는법(root 권한)
```prompt
sudo su
```
## docker 설치
1. 우분투 시스템 패키지 업데이트
```prompt
sudo apt-get update
```

2. 필요한 패키지 설치
```prompt
sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
```

3. Docker 공식 GPG 키 추가
```prompt
sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
```

4. Docker의 공식 apt 저장소 추가
```prompt
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```
5. 시스템 패키지 업데이트
```prompt
sudo apt-get update
```
6. Docker 설치
```prompt
sudo apt-get install docker-ce docker-ce-cli containerd.io
```



### docker pull
* Doker hub 에 push한 이미지를 가져오기 위하여 pull 합니다.
```prompt
 docker pull jeamin08/myrepo:1.0.0
```

### docker 컨테이너 시작
`` docker images
여기서 나온 이미지 이름을 명령어 뒤에 넣으면 서버배포 완료.

```
docker run -i -t -p 8080:8080 jeamin08/myrepo:1.0.0
```
### 만약 빌드할때 sql 28000 에러가 뜬다면??

1. 비밀번호 틀림
2. root 계정 접속권한문제
보통 이 2가지이다.

#### 1 번일 경우
> yml 설정을 우분투 계정 비밀번호에 맞게 바꾸면 된다.

#### 2 번의 경우 
> 모든 ip 를 허용하는 root 계정을 하나 만든다.

1. root@% 사용자 추가
```sql
CREATE USER 'root'@'%' IDENTIFIED BY 'your_new_password';

```

2. 권한 부여
```sql
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
```
3. 권한 적용
```
FLUSH PRIVILEGES;
```



### 추가 명령어

#### docker 컨테이너,이미지등 정보를 보여줌 (중지된것을 포함한 모든 이름)
```
docker ps 
```

##### docker ps 명령어로 나온 중지된 컨테이너 실행
```
docker start {컨테이너 ID}
```

##### container 중지
```
docker stop {컨테이너 이름}
```