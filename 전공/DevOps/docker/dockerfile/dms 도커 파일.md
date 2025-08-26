
## 멀티 스테이지 dockerfile (한곳은 빌드만 하고, 하나는 빌드된 jar 파일을 실행만 시킴)
* 1번째 컨테이너 -> 알파인 + JDK 환경 -> jdk 로 빌드하여 jar 파일 까지 생성 -> 컨테이너 삭제
* 2번째 컨테이너 -> JRE 환경 -> 빌드된 jar 파일 실행
이렇게 하는 이유
> 이미지 크기를 최대한으로 줄이기 위해


``` dockerfile

#  알파인 리눅스(초경량 리눅스) + open jdk 17 환경으로 컨테이너 생성.
FROM eclipse-temurin:17-jdk-alpine AS build  
# 컨테이너 안에서 /app 이라는 폴더 생성
WORKDIR /app  
# 실행환경이 alpine 이라 gradle 실행하기에 충분한 환경이 아님
# -> 필요한 것들 추가로 설치
RUN apk add --no-cache bash findutils gcompat  
# 빌드되고 바로 종료될 컨테테이너라 도커 데몬 생성 비활성화, 캐시 활성화 옵션, 모듈 병렬 빌드 네트워크 타임아웃 시간 늘리기, gradle jvm 최적화 옵션 등(gradle 은 java 로 짜여진 프로그램임).
ENV GRADLE_OPTS="-Dorg.gradle.daemon=false -Dorg.gradle.parallel=true -Dorg.gradle.configureondemand=true -Dorg.gradle.caching=true -Dorg.gradle.configuration-cache=true -Dorg.gradle.unsafe.configuration-cache=true -Dorg.gradle.unsafe.configuration-cache-problems=warn -Dorg.gradle.workers.max=4 -Dorg.gradle.logging.level=lifecycle -XX:+UseContainerSupport -XX:MaxRAMPercentage=75.0 -XX:+UseG1GC -XX:G1HeapRegionSize=16m -XX:+UseStringDeduplication -XX:-UsePerfData -XX:+DisableExplicitGC -XX:MaxMetaspaceSize=512m -Dkotlin.compiler.execution.strategy=in-process -Dkotlin.incremental=false -Dkotlin.daemon.jvm.options=-Xmx1g -Dkotlin.parallel.tasks.in.project=true -Dfile.encoding=UTF-8 -Duser.country=US -Duser.language=en -Duser.timezone=UTC -Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom -Dorg.gradle.internal.http.connectionTimeout=60000 -Dorg.gradle.internal.http.socketTimeout=60000 -Dorg.gradle.internal.repository.max.tentatives=1 -Dorg.gradle.internal.repository.initial.backoff=500 -Dorg.gradle.internal.network.retry.max.times=2"

# 빌드하면서 나온 의존성파일, 캐시파일등을 레이어로 만들어서 컨테이너 재시작되도 캐시가 안정적으로 남아있게 하기 위해 캐시 저장 경로 명시
ENV GRADLE_USER_HOME="/cache/.gradle" 
# 컨테이너에 캐시 전용 폴더 생성
RUN mkdir -p /cache/.gradle  

# docker build 시 보내진 프로젝트 파일 컨테이너로 복사.
COPY ./build.gradle* ./settings.gradle* ./gradle.properties* ./gradlew* ./  
# 기존 프젝에서 사용되었던 
COPY ./gradle gradle  
RUN chmod +x gradlew && ./gradlew --version  
RUN chmod +x gradlew && ./gradlew dependencies || true  
COPY ./ .  
RUN chmod +x gradlew && ./gradlew clean bootJar -x test && \  
    rm -rf .gradle /app/.gradle /root/.kotlin /tmp/* /var/tmp/* /tmp/kotlin-daemon*.log* 2>/dev/null || true  
  
FROM eclipse-temurin:17-jre-alpine  
RUN apk add --no-cache tzdata && addgroup -g 1001 appgroup && adduser -u 1001 -G appgroup -s /bin/sh -D appuser  
WORKDIR /app  
  
EXPOSE 8080  
ENV TZ=Asia/Seoul JAVA_OPTS="-XX:+UseContainerSupport -XX:MaxRAMPercentage=75.0 -XX:+UseG1GC -XX:G1HeapRegionSize=16m -XX:+UseStringDeduplication -XX:-UsePerfData"  
  
HEALTHCHECK --interval=10s --timeout=5s --start-period=30s --retries=3 CMD curl -f http://localhost:8080/health || exit 1  
  
COPY --from=build --chown=appuser:appgroup /app/dms-main/main-infrastructure/build/libs/*.jar /tmp/libs/  
RUN find /tmp/libs -name "*.jar" ! -name "*-plain.jar" -exec cp {} /app/app.jar \; && rm -rf /tmp/libs && chown appuser:appgroup /app/app.jar  
USER appuser  
ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS -jar /app/app.jar"]
```