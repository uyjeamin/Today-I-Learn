깃허브 액션 -> 자바 스프링 관련 gradle.yml 파일로 선택후 생성
(자동으로 생성되는것으로 하면 루트묘듈이랑 위치가 같아질거임.)


>[!note] 지금 이 파일은 스프링 application yml 파일에서 redis 와 mysql 이 둘다 localhost 로 되어있을때 작동함. 테스트코드까지 자동으로 실행시키는 ci 파일



```java
name: Java CI with Gradle 

on: # 트리거
  push:
    branches: [ "develop" ] # develop 에 push 될때
  pull_request:
    branches: [ "develop" ] # develop 에 pr 될때

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: read

    services:# mysql 과 redis 컨테이너를 깃허브 엑션 러너에 올림 
      mysql: 
        image: mysql:8.0
        env:
          MYSQL_ROOT_PASSWORD: 1234
          MYSQL_DATABASE: dabjeongneo
        ports:
         강체크
          --health-interval=30s
          --health-timeout=10s
          --health-retries=3

      redis:
        image: redis:latest
        ports:
          - 6379:6379
        options: >-
          --health-cmd="redis-cli ping" # 건강체크
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

    - name: Setup Gradle # gradle 명령어 쓸 수 있도록
      uses: gradle/actions/setup-gradle@v4

    - name: Check for Gradle Wrapper
      run: ls -l dabjeongneo/gradlew  # gradlew 존재 여부 확인

    - name: Grant execute permission for gradlew
      run: chmod +x dabjeongneo/gradlew  # gradlew 실행 권한 부여

    - name: Build with Gradle Wrapper
      working-directory: dabjeongneo  # Gradle 빌드 디렉토리 설정
      run: ./gradlew build
      
	# 로그들 출력
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



