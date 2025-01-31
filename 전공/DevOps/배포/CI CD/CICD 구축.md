깃허브 엑션의 환경
* 각 run: 마다 독립적인 셀(다른 프롬프트 창) 에서 실행됨
* 각 job 은 동기적으로 실행되게 새팅된것이 아니면 독립된 runner 환경(깃허브 액션의 가상컴퓨터) 에서 비동기적으로 실행됨.

[[CI 구축]] 에서 test 용으로 러너에 실리는 컨테이너들의 로그출력을 빼고, CD 과정을 추가한 파일이다

## CD 과정

1. CI 에서 빌드시 스프링 프로젝트에 있는 도커파일로 인해 jkd 17 과 함께 이미지로 만들어 진다 -> 도커파일 필요함
2. 그 이미지를 docker hub 로그인 후 push 한다.
3. appleboy/ssh-action@master 를 이용해 ec2 서버에 ssh.pem 키로 접속한다.
4. ec2 서버에 docker 와 docker compose 없다면 자동으로 설치한다. (있을시 설치 안함) 
5. 깃허브 시크릿에 있는 환경변수에 있는 compose 텍스트를docker-compose.yml 파일로 옮긴다.
6. docker-compose.yml 에서 내부적으로 docker pull 을 하기 때문에 **docker hub 로그인**을 한다. (각 run: 마다 독립적인 환경에서 실행되기 때문에 이전의 docker login 한 내용이 다른 run 이나 script 까지 이여지지 않음.)
7. docker-compose 파일을 실행한다 compose 파일안에 이미지가 만약 변경된다면 기존 도커 컨테이너를 지우고 변경된 이미지로 다시 실행함. 변경되지 않았다면 그냥 멈췄다가 재실행.



CIDI 하는법.

1. 일단 스프링 프로젝트에 docker 파일을 만들자 

```Dockerfile
# 기본 OpenJDK 이미지 사용  FROM openjdk:17-jdk    
FROM openjdk:17-jdk

# docker 컨테이너의 작업 디렉토리(없으면 이 이름으로 자동생성)  
WORKDIR /spring-boot  
  
# build/libs/*SNAPSHOT.jar 이 경로에 있는 jar 파일을 app.jar 파일에 옮김  
COPY build/libs/*SNAPSHOT.jar app.jar  
  
# 도커 컨테이너 실행시  
ENTRYPOINT ["java", "-jar", "/spring-boot/app.jar"]
```





2. 깃허브 엑션에 이 파일 넣기

```action
name: Java CICD gradle

on: # 트리거거
  push:
    branches: [ "main" ] 
  pull_request:
    branches: [ "main" ]

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: read

    services:
      mysql: # test 용
        image: mysql:8.0
        env:
          MYSQL_ROOT_PASSWORD: 1234 
          MYSQL_DATABASE: dabjeongneo
        ports:
          - 3306:3306
        options: >-
          --health-cmd="mysqladmin ping -h mysql"
          --health-interval=30s
          --health-timeout=10s
          --health-retries=3

      redis: # test 용
        image: redis:latest
        ports:
          - 6379:6379
        options: >-
          --health-cmd="redis-cli ping"
          --health-interval=30s
          --health-timeout=10s
          --health-retries=3

    steps:
    - uses: actions/checkout@v4

    - name: Check directory structure
      run: ls -R  # 디렉토리 구조 확인

    - name: Set up JDK 17
      uses: actions/setup-java@v4
      with:
        java-version: '17'
        distribution: 'temurin'

    - name: Setup Gradle
      uses: gradle/actions/setup-gradle@v4

    - name: Check for Gradle Wrapper
      run: ls -l dabjeongneo/gradlew  # gradlew 존재 여부 확인

    - name: Grant execute permission for gradlew
      run: chmod +x dabjeongneo/gradlew  # gradlew 실행 권한 부여

    - name: Build with Gradle Wrapper
      working-directory: dabjeongneo  # Gradle 빌드 디렉토리 설정
      run: ./gradlew build

    - name: Docker login
      uses: docker/login-action@v3 
      with: 
        username: ${{ secrets.DOCKER_HUB_USERNAME }} 
        password: ${{ secrets.DOCKER_HUB_PASSWORD }}

    - name: Build and Push Docker images
      working-directory: dabjeongneo
      run: |
        docker build -f Dockerfile -t ${{ secrets.DOCKER_HUB_REPO }} .
        docker push ${{ secrets.DOCKER_HUB_REPO }}

  deploy:
    needs: build
    runs-on: ubuntu-latest
    permissions: 
      write-all
    steps:
      - name: Deploy to server
        uses: appleboy/ssh-action@master
        env:
          COMPOSE: ${{ secrets.DOCKER_COMPOSE }} 
        with:
          host: ${{ secrets.HOST }}
          username: ubuntu
          port: 22
          key: ${{ secrets.PEM_KEY }}
          envs: GITHUB_SHA
          script: |
            
            # Docker & Docker Compose 설치 확인 및 설치
            if ! command -v docker &> /dev/null
            then
              echo "Docker를 설치합니다..."
              sudo apt update
              sudo apt install -y docker.io
              sudo systemctl enable docker
              sudo systemctl start docker
            else
              echo "Docker가 이미 설치됨"
            fi

            if ! command -v docker-compose &> /dev/null
            then
              echo "Docker Compose를 설치합니다..."
              sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
              sudo chmod +x /usr/local/bin/docker-compose
            else
              echo "Docker Compose가 이미 설치됨"
            fi

            #  Docker 비상호작용 로그인 처리
            sudo docker login -u ${{ secrets.DOCKER_HUB_USERNAME }} -p ${{ secrets.DOCKER_HUB_PASSWORD }}


            #  Docker Compose 실행
            sudo docker-compose  up -d

```




3. 깃허브 시크릿에 값 설정해 주기 
![[Pasted image 20250131122831.png]]
1. 도커컴포즈파일
2. 도커허브 비번
3. 도커허브 레포(사용자이름/레포:버전)
버전을 명시안하고 레포까지만 쓰면 자동으로 latest 로 되서 가장 최신꺼로 pull 됨.
4. 도커허브 유저네임
5. ec2 호스트


#### compose 파일은 이러함.
```compose

version: '3'

services:
  database:
    container_name: mysql_db
    image: "mysql/mysql-server:5.7"
    restart: unless-stopped
    environment:
      MYSQL_DATABASE: user_db
      MYSQL_ROOT_HOST: '%'
      MYSQL_ROOT_PASSWORD: 1234
      TZ: 'Asia/Seoul'
    ports:
      - "3307:3306"
    volumes:
      - mysql_data:/var/lib/mysql
    command:
      - "mysqld"
      - "--character-set-server=utf8mb4"
      - "--collation-server=utf8mb4_unicode_ci"
    networks:
      - test_network # 네트워크 이름 명시(이 네트워크 통해서 컨테이너끼리 통신)

  application:
    container_name: docker-compose-test
    restart: on-failure
    image: "jeamin08/dabjeongneo" # 이부분은 자기 래포에 맞춰서
    ports:
      - "8080:8080"
    environment:
      SPRING_DATASOURCE_URL: jdbc:mysql://database:3306/user_db 
      SPRING_DATASOURCE_USERNAME: root
      SPRING_DATASOURCE_PASSWORD: 1234
      SPRING_REDIS_HOST: redis # 호스트 이름
      SPRING_PORT: 6379 # 호스트 기준 내부 포트
    depends_on:
      - database
      - redis
    networks:
      - test_network

  redis:
    container_name: redis
    restart: unless-stopped
    image: "redis:latest"
    ports: 
     - "6380:6379"
    volumes:
      - redis_data:/var/lib/redis
    networks:
      - test_network  # 이 네트워크에 명시해주면 됨

volumes:
  mysql_data:
  redis_data:

networks:
  test_network: {}  # 네트워크를 이렇게 한번만 정의하면 됨


```


