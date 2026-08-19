# --- 共通のベースイメージ ---
FROM eclipse-temurin:21-jdk-jammy AS base
WORKDIR /app
COPY . .

# --- 開発環境用（devモード） ---
FROM base AS dev
CMD ./gradlew bootRun -x test --args="--spring.profiles.active=dev"
# --- 本番環境のビルド用（ご提示いただいた部分） ---
FROM base AS build
RUN ./gradlew bootJar -x test

# --- 本番環境の実行用 ---
FROM eclipse-temurin:21-jre-jammy AS prod
WORKDIR /app
COPY --from=build /app/build/libs/*.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar", "--spring.profiles.active=prod"]
