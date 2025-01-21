깃허브 액션 -> 자바 스프링 관련 gradle.yml 파일로 선택후 생성
(자동으로 생성되는것으로 하면 루트묘듈이랑 위치가 같아질거임.)

```java
name: Java CI with Gradle

on:
  push:
    branches: [ "develop" ]
  pull_request:
    branches: [ "develop" ]

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: read

    services: // 토커 compose 로 띄움
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
```


지금 이 파일은 스프링 application yml 파일에서 redis 와 mysql 이 둘다 localhost 로 되어있을때 작동함. 테스트코드까지 자동으로 실행시키는 ci 파일