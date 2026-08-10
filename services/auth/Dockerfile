FROM maven:3.9-eclipse-temurin-21 AS build
WORKDIR /app
COPY pom.xml .
COPY services/auth/pom.xml services/auth/pom.xml
RUN mvn -B -f services/auth/pom.xml dependency:go-offline
COPY services/auth/src services/auth/src
RUN mvn -B -f services/auth/pom.xml clean package -DskipTests

FROM eclipse-temurin:21-jre
WORKDIR /app
COPY --from=build /app/services/auth/target/*.jar app.jar
EXPOSE 8081
ENTRYPOINT ["java", "-jar", "/app/app.jar"]
