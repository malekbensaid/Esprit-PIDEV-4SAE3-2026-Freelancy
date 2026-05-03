# ===== BUILD =====
FROM maven:3.9.9-eclipse-temurin-17 AS build

WORKDIR /app

COPY smart-freelance-backend/pom.xml .
COPY smart-freelance-backend/src ./src

RUN mvn clean package -DskipTests

# ===== RUN =====
FROM eclipse-temurin:17-jre-jammy

WORKDIR /app

COPY --from=build /app/target/*.jar app.jar

EXPOSE 8092

ENTRYPOINT ["java", "-jar", "app.jar"]
