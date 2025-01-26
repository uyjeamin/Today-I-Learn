
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


```name: Java CICD gradle

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
            sudo docker login -u ${{ secrets.DOCKER_USERNAME }} -p ${{ secrets.DOCKER_PASSWORD }}

            #  docker-compose.yml 파일 확인
            if [ ! -f docker-compose.yml ]; then
              echo "docker-compose.yml 파일이 없습니다. 파일을 확인하세요."
              exit 1
            fi

            #  기존 컨테이너 제거 (컨테이너가 있을 경우에만)
            if [ "$(docker ps -a -q)" ]; then
              sudo docker rm $(docker ps -a -q)
            else
              echo "삭제할 컨테이너가 없습니다."
            fi

            #  기존 이미지 제거 (이미지가 있을 경우에만)
            if [ "$(docker images -q)" ]; then
              sudo docker rmi $(docker images -q)
            else
              echo "삭제할 이미지가 없습니다."
            fi

            #  Docker Compose 실행
            sudo docker-compose up -d



    
```




compose
```

version: '3'

services:
  database:
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
    environment:
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
      - test_network  # 이 네트워크에 명시해주면 됨

volumes:
  mysql_data:
  redis_data:

networks:
  test_network: {}  # 네트워크를 이렇게 한번만 정의하면 됨


```



docker compose
