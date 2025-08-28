
## 멀티 스테이지 dockerfile (한곳은 빌드만 하고, 하나는 빌드된 jar 파일을 실행만 시킴

## Build Stage

실행순서
베이스 이미지 : 알파인 + JDK 환경 -> jdk 로 빌드하여 jar 파일 까지 생성 (컨테이너로 실행은 안함.)

⇒ 빌드후 jar 파일 Run Stage 에서 가져가게 해주고 사라지는 임시 이미지.

## Run Stage

실행순서
베이스 이미지 : 알파인 + JRE 환경 -> 1번째 빌드된 이미지에 접근하여 빌드된 jar 파일 복사해옴 -> 이미지 실행

⇒ Build Stage 에서 jar 파일 가져와서 최종적으로 실행되는 이미지.
이렇게 하는 이유
## 장점

- 이미지 크기 최소화
- 빌드 시간 최소화
- 보안성

```dockerfile
## 빌드 전용 스테이지(build stage) ##
# 알파인 리눅스(초경량 리눅스) + open jdk 17 환경으로 컨테이너 생성.
# AS <이름> 임시로 참조하고 버릴 이미지
FROM eclipse-temurin:17-jdk-alpine AS build 

# 컨테이너 안에서 /app 이라는 폴더 생성
WORKDIR /app

# 실행환경이 alpine 이라 gradle 실행하기에 충분한 환경이 아님
# -> 필요한 것들 추가로 설치(bash, findutils, gcompat)
RUN apk add --no-cache bash findutils gcompat

# 빌드되고 바로 종료될 컨테이너라 도커 데몬 생성 비활성화, 
# 캐시 활성화 옵션, 모듈 병렬 빌드, 네트워크 타임아웃 시간 늘리기, 
# gradle 실행용 jvm 최적화 옵션 등
ENV GRADLE_OPTS="\
-Dorg.gradle.daemon=false \
-Dorg.gradle.parallel=true \
-Dorg.gradle.configureondemand=true \
-Dorg.gradle.caching=true \
-Dorg.gradle.configuration-cache=true \
-Dorg.gradle.unsafe.configuration-cache=true \
-Dorg.gradle.unsafe.configuration-cache-problems=warn \
-Dorg.gradle.workers.max=4 \
-Dorg.gradle.logging.level=lifecycle \
-XX:+UseContainerSupport \
-XX:MaxRAMPercentage=75.0 \
-XX:+UseG1GC \
-XX:G1HeapRegionSize=16m \
-XX:+UseStringDeduplication \
-XX:-UsePerfData \
-XX:+DisableExplicitGC \
-XX:MaxMetaspaceSize=512m \
-Dkotlin.compiler.execution.strategy=in-process \
-Dkotlin.incremental=false \
-Dkotlin.daemon.jvm.options=-Xmx1g \
-Dkotlin.parallel.tasks.in.project=true \
-Dfile.encoding=UTF-8 \
-Duser.country=US \
-Duser.language=en \
-Duser.timezone=UTC \
-Djava.awt.headless=true \
-Djava.security.egd=file:/dev/./urandom \
-Dorg.gradle.internal.http.connectionTimeout=60000 \
-Dorg.gradle.internal.http.socketTimeout=60000 \
-Dorg.gradle.internal.repository.max.tentatives=1 \
-Dorg.gradle.internal.repository.initial.backoff=500 \
-Dorg.gradle.internal.network.retry.max.times=2 \
"

# Gradle 캐시 디랙토리 지정.
ENV GRADLE_USER_HOME="/cache/.gradle"
# 캐시용 디랙토리 생성.
RUN mkdir -p /cache/.gradle

# gradle 세팅, 의존성, 의존성 버전, gradlew 실행 스크립트 파일 가져오기.
COPY ./build.gradle* ./settings.gradle* ./gradle.properties* ./gradlew* ./
COPY ./gradle ./gradle
COPY ./buildSrc ./buildSrc

# gradlew 스크립트 Linux 환경에서 줄바꿈 인식 오류 해결
RUN sed -i 's/\r$//' gradlew && chmod +x gradlew

# 의존성만 미리 다운로드 -> 레이어 캐시를 최대한 활용하여, 다음 docker build 시에도 gradle 캐시가 남아있게 하여 빌드 속도가 빨라지기 위해. (실패해도 BootJar 빌드시 캐시된 의존성 없으면 다운로드됨으로 || true)
RUN ./gradlew dependencies || true

# 모노레포 구조에서 실행할 컨텍스트만 복사
COPY ./dms-main ./dms-main
# 테스트 없이 fatJar 빌드 
# gradle 캐시, 코틀린 컴파일러 캐시 등 제거, 제거시 문제가 일어나도 docker build 엔 문제 없게 || true
RUN ./gradlew :dms-main:main-infrastructure:clean :dms-main:main-infrastructure:bootJar -x test && \
    rm -rf .gradle /app/.gradle /root/.kotlin /tmp/* /var/tmp/* /tmp/kotlin-daemon*.log* 2>/dev/null || true


## 실행 전용 스테이지(run stage) ##
# 알파인 + JRE 환경 (실행할때는 JDK 대신 JRE 만 있으면 됨.)
FROM eclipse-temurin:17-jre-alpine

# 타임존 캐쉬없이 설치(리눅스 시간 한국으로 맞추기 위해), 
# 일반 사용자 만들기 (리눅스에선 사용자가 무조건 그룹 안에 속해 있어야함 
# -> 리눅스 사용자 그룹 생성후, 사용자 소속시키기)
RUN apk add --no-cache tzdata && \
    addgroup -g 1001 appgroup && \
    adduser -u 1001 -G appgroup -s /bin/sh -D appuser

# copy, run 등 명령어에서 경로지정없이 /app 을 기준으로 동작
WORKDIR /app

# 리눅스 시간대 서울로 맞추기
# jvm 튜닝 (가비지컬랙션 G1사용, heap 영역 컨테이너 메모리의 75% 까지만 사용,
# 힙을 16mb 로 나누어서 Stop The World 타입 최소화, JVM 퍼포먼스 로그 끄기)
ENV TZ=Asia/Seoul
ENV JAVA_OPTS="\
-XX:+UseContainerSupport \
-XX:MaxRAMPercentage=75.0 \
-XX:+UseG1GC \
-XX:G1HeapRegionSize=16m \
-XX:+UseStringDeduplication \
-XX:-UsePerfData \
"

# build stage 에서 빌드한 jar 파일 가져옴.
COPY --from=build --chown=appuser:appgroup /app/dms-main/main-infrastructure/build/libs/*.jar /tmp/libs/
# plain jar 가 혹시라도 포함되어 있으면 제외하고 fat jar 만 가져오기, 
# 파일 소유권 appuser 에게 부여.
RUN find /tmp/libs -name "*.jar" ! -name "*-plain.jar" -exec cp {} /app/app.jar \; && \
    rm -rf /tmp/libs && \
    chown appuser:appgroup /app/app.jar

# 이 컨테이너는 8080 포트를 이용한다고 문서화(메타데이터)
EXPOSE 8080

USER appuser

# healthcheck
HEALTHCHECK --interval=10s --timeout=5s --start-period=30s --retries=3 CMD curl -f http://localhost:8080/ || exit 1

# jvm 옵션 적용해서, 이미지 시작하자 마자 jar 파일 실행.
ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS -jar /app/app.jar"]


```