
## 멀티 스테이지 dockerfile (한곳은 빌드만 하고, 하나는 빌드된 jar 파일을 실행만 시킴)
* 1번째 컨테이너 -> 알파인 + JDK 환경 -> jdk 로 빌드하여 jar 파일 까지 생성 -> 컨테이너 삭제
* 2번째 컨테이너 -> JRE 환경 -> 빌드된 jar 파일 실행
이렇게 하는 이유
> 이미지 크기를 최대한으로 줄이기 위해


``` dockerfile

## 빌드 전용 컨테이너(build stage) ##

#  알파인 리눅스(초경량 리눅스) + open jdk 17 환경으로 컨테이너 생성.
FROM eclipse-temurin:17-jdk-alpine AS build  
# 컨테이너 안에서 /app 이라는 폴더 생성
WORKDIR /app  
# 실행환경이 alpine 이라 gradle 실행하기에 충분한 환경이 아님
# -> 필요한 것들 추가로 설치(bash, findutils, gcompat)
RUN apk add --no-cache bash findutils gcompat  
# 빌드되고 바로 종료될 컨테테이너라 도커 데몬 생성 비활성화, 캐시 활성화 옵션, 모듈 병렬 빌드 네트워크 타임아웃 시간 늘리기, gradle jvm 최적화 옵션 등(gradle 은 java 로 짜여진 프로그램임).
ENV GRADLE_OPTS="-Dorg.gradle.daemon=false -Dorg.gradle.parallel=true -Dorg.gradle.configureondemand=true -Dorg.gradle.caching=true -Dorg.gradle.configuration-cache=true -Dorg.gradle.unsafe.configuration-cache=true -Dorg.gradle.unsafe.configuration-cache-problems=warn -Dorg.gradle.workers.max=4 -Dorg.gradle.logging.level=lifecycle -XX:+UseContainerSupport -XX:MaxRAMPercentage=75.0 -XX:+UseG1GC -XX:G1HeapRegionSize=16m -XX:+UseStringDeduplication -XX:-UsePerfData -XX:+DisableExplicitGC -XX:MaxMetaspaceSize=512m -Dkotlin.compiler.execution.strategy=in-process -Dkotlin.incremental=false -Dkotlin.daemon.jvm.options=-Xmx1g -Dkotlin.parallel.tasks.in.project=true -Dfile.encoding=UTF-8 -Duser.country=US -Duser.language=en -Duser.timezone=UTC -Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom -Dorg.gradle.internal.http.connectionTimeout=60000 -Dorg.gradle.internal.http.socketTimeout=60000 -Dorg.gradle.internal.repository.max.tentatives=1 -Dorg.gradle.internal.repository.initial.backoff=500 -Dorg.gradle.internal.network.retry.max.times=2"

# 빌드하면서 나온 의존성파일, 캐시파일등을 레이어로 만들어서 컨테이너 재시작되도 캐시가 안정적으로 남아있게 하기 위해 캐시 저장 경로 명시
ENV GRADLE_USER_HOME="/cache/.gradle" 
# 컨테이너에 캐시 전용 폴더 생성
RUN mkdir -p /cache/.gradle  

# gradle 의존성, 설정파일만 복사해서 컨테이너로 가져옴.
COPY ./build.gradle* ./settings.gradle* ./gradle.properties* ./gradlew* ./  
# 기존 프젝에 맞춰서 다운받아진 gradle 복사.
COPY ./gradle gradle  

# gradlew 권한 부여, 실행 테스트
RUN chmod +x gradlew && ./gradlew --version  

# build.gradle 을 읽고, 필요한 의존성 다운로드 후 트리형태로 출력 의존성 미리 다운 실패하여도 ./gradlew bootJar 에서 다운받으면 되니 항상 성공하게 || true 
# 먼저 의존성 다운하는 이유 -> docker build 를 할때 이전과 build.gradle 의 의존성이 변경되지 않았으면 기존에 다운로드 한 의존성 재사용 하기 위해 -> build 속도 업
# 이전에 생성한 /cache/.gradle 에 다운한 의존성 파일 저장
RUN chmod +x gradlew && ./gradlew dependencies || true  

# 지정한 프로젝트 컨텍스트 전체 복사
COPY ./ .  

# ./gradlew bootJar 모든 의존성 포함한 fat JAR 생성
# gradle 캐시, 코틀린 컴파일러 캐시, gradle 데몬등이 남기는 로그등 전부 삭제, 삭제하려는 폴더가 없을시 오류 안나게 항상 성공표시 되도록 || true 추가
RUN chmod +x gradlew && ./gradlew clean bootJar -x test && \  
    rm -rf .gradle /app/.gradle /root/.kotlin /tmp/* /var/tmp/* /tmp/kotlin-daemon*.log* 2>/dev/null || true  
  
  
## 실행 전용 컨테이너(runtime stage) ##
  
# 알파인 + JRE 환경 (실행할때는 JDK 대신 JRE 만 있으면 됨.)
FROM eclipse-temurin:17-jre-alpine  

# 타임존 캐쉬없이 설치(리눅스 시간 한국으로 맞추기 위해), 일반 사용자 만들기 (리눅스에선 사용자가 무조건 그룹 안에 속해 있어야함 -> 리눅스 사용자 그룹 생성후, 사용자 소속시키기)
RUN apk add --no-cache tzdata && addgroup -g 1001 appgroup && adduser -u 1001 -G appgroup -s /bin/sh -D appuser  

# copy, run 등 명령어에서 경로지정없이 /app 을 기준으로 동작
WORKDIR /app  

# 이 컨테이너는 8080 포트를 이용한다고 문서화(메타데이터)
EXPOSE 8080  

# 리눅스 시간대 서울로 맞추기
# jvm 튜닝 (가비지컬랙션 G1사용, heap 영역 컨테이너 메모리의 75% 까지만 사용,  )
ENV TZ=Asia/Seoul JAVA_OPTS="-XX:+UseContainerSupport -XX:MaxRAMPercentage=75.0 -XX:+UseG1GC -XX:G1HeapRegionSize=16m -XX:+UseStringDeduplication -XX:-UsePerfData"  

  
HEALTHCHECK --interval=10s --timeout=5s --start-period=30s --retries=3 CMD curl -f http://localhost:8080 || exit 1  
  
COPY --from=build --chown=appuser:appgroup /app/dms-main/main-infrastructure/build/libs/*.jar /tmp/libs/  
RUN find /tmp/libs -name "*.jar" ! -name "*-plain.jar" -exec cp {} /app/app.jar \; && rm -rf /tmp/libs && chown appuser:appgroup /app/app.jar  
USER appuser  
ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS -jar /app/app.jar"]
```