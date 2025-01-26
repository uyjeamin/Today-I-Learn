
1. IAM 사용자 생성

### IAM 사용자란?  -> 일부 필요한 권한만 준 계정

>[!note] 루트계정을 그대로 쓰면 보안상 아주 위험하기 때문에 IAM 사용자를 만든다
>

IAM -> 사용자 -> 사용자 생성

이름 입력 후

``[] IAM 사용자를 생성하고 싶어요 
칸 클릭

권한 설정은 **직접 정책 연결로**

**정책은** 
## EC2 만 쓴다면 ?
``AmazonEC2FullAccess

## S3 도 쓴다면 ?
``AmazonS3FullAccess

## ECR 도 쓴다면 ?
``AmazonECRFullAccess

나는 일단 다하긴 한거 같다 근데 EC2 만 쓸거여서 
``AmazonEC2FullAccess 
이것만 해도 충분할듯


```
name: Java CI with Gradle

on:
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
      mysql:
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

      redis:
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

    - name: Wait for MySQL to be ready
      run: |
        echo "Waiting for MySQL to start"
        sleep 30

    - name: Wait for Redis to be ready
      run: |
        echo "Waiting for Redis to start"
        sleep 30

    - name: Print MySQL container logs
      run: |
        CONTAINER_ID=$(docker ps -q --filter "ancestor=mysql:8.0")
        echo "MySQL container logs:"
        docker logs $CONTAINER_ID

    - name: Print Redis container logs
      run: |
        CONTAINER_ID=$(docker ps -q --filter "ancestor=redis:latest")
        echo "Redis container logs:"
        docker logs $CONTAINER_ID
        
    - name: docker login 
      uses: docker/login-action@v3 
      with: 
	    username: ${{ secrets.DOCKER_HUB_USERNAME }} 
	    password: ${{ secrets.DOCKER_HUB_PASSWORD }} 
	    
	- name: Build Push Docker images
	  run: |
 	    docker build -f Dockerfile -t ${{ secrets.DOCKER_REPO }} .
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
	    key: ${{ secrets.KEY }}
	    envs: GITHUB_SHA
	    script: |
	      # 도커 로그인
	      sudo docker login -u ${{ secrets.DOCKER_USERNAME }} -p ${{ secrets.DOCKER_PASSWORD }}
      
	      # 프로젝트 경로로 이동
	      cd ${{secrets.PROJECT_PATH}}
      
	      # docker-compose.yml 파일 생성 (COMPOSE 환경 변수를 사용)
	      echo "$COMPOSE" > docker-compose.yml
      
	      # 기존 도커 컨테이너 및 이미지를 내려놓고 제거
	      sudo docker-compose down
	      sudo docker rm $(sudo docker ps -a -q)
	      sudo docker rmi $(sudo docker images -q)
      
	      # 새로운 도커 이미지를 풀
	      sudo docker pull ${{secrets.DOCKER_REPO }}
      
	      # 새로운 docker-compose를 사용해 컨테이너 시작
	      sudo docker-compose up --build -d


    
```




compose
```

version: '3'

services:
  database: # 서비스 이름 = 호스트이름름
    container_name: mysql_db
    image: mysql/mysql-server:5.7
    restart: unless-stopped
    environment:
      MYSQL_DATABASE: users_db
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
    image: ${DOCKER_HUB_REPO} # 이부분은 자기 래포에 맞춰서

    ports:
      - "8080:8080"
    environment:                        # 호스트 이름으로 접근
      SPRING_DATASOURCE_URL: jdbc:mysql://database:3306:user_db 
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
    image: redis:latest
    ports: 
     - "6380:6379"
    volumes:
      - redis_data:/var/lib/redis

networks:
  test_network: 

volumes:
  mysql_data:
  redis_data:

```

jeamin/dabjeongneo