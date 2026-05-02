FROM maven:3.9.9-eclipse-temurin-17 AS build
WORKDIR /app

COPY service/pom.xml ./pom.xml
COPY service/src ./src

RUN set -eux; \
    mvn -B -DskipTests package; \
    JAR_FILE="$(ls -1 target/*.jar | grep -Ev '(sources|javadoc)\\.jar$' | head -n 1)"; \
    test -n "$JAR_FILE"; \
    cp "$JAR_FILE" /app/app.jar

FROM eclipse-temurin:17-jre-jammy
WORKDIR /app

RUN set -eux; \
    useradd -r -u 10001 -s /usr/sbin/nologin appuser; \
    chown -R appuser:appuser /app

COPY --from=build --chown=appuser:appuser /app/app.jar /app/app.jar

USER 10001
EXPOSE 8092
ENTRYPOINT ["java","-jar","/app/app.jar"]
