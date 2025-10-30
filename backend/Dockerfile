# ========== 1) Build stage ==========
FROM eclipse-temurin:21-jdk AS build
WORKDIR /app

# Gradle Wrapper와 빌드 스크립트 먼저 복사(캐시 최적화)
COPY gradlew ./
COPY gradle gradle
COPY settings.gradle.kts build.gradle.kts ./
RUN chmod +x gradlew

# 의존성 미리 내려 캐시(소스 이전 단계)
RUN ./gradlew dependencies --no-daemon || true

# 소스 복사
COPY src src

# 애플리케이션 빌드 (테스트 생략은 선택)
RUN ./gradlew clean bootJar --no-daemon -x test

# ========== 2) Runtime stage ==========
FROM eclipse-temurin:21-jre
WORKDIR /app
COPY --from=build /app/build/libs/*.jar app.jar
# 필요하면 환경파일도 복사
# COPY --from=build /app/.env .env

EXPOSE 8080
ENTRYPOINT ["java","-Dspring.profiles.active=prod","-jar","/app/app.jar"]