FROM maven:3.9-eclipse-temurin-21 AS build
WORKDIR /app
COPY pom.xml .
COPY services/inventory/pom.xml services/inventory/pom.xml
RUN mvn -B -f services/inventory/pom.xml dependency:go-offline
COPY services/inventory/src services/inventory/src
RUN mvn -B -f services/inventory/pom.xml clean package -DskipTests

FROM eclipse-temurin:21-jre
WORKDIR /app
COPY --from=build /app/services/inventory/target/*.jar app.jar
EXPOSE 8082
ENTRYPOINT ["java", "-jar", "/app/app.jar"]
