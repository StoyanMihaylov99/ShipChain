FROM maven:3.9-eclipse-temurin-21 AS build
WORKDIR /app
COPY pom.xml .
COPY services/payment/pom.xml services/payment/pom.xml
RUN mvn -B -f services/payment/pom.xml dependency:go-offline
COPY services/payment/src services/payment/src
RUN mvn -B -f services/payment/pom.xml clean package -DskipTests

FROM eclipse-temurin:21-jre
WORKDIR /app
COPY --from=build /app/services/payment/target/*.jar app.jar
EXPOSE 8083
ENTRYPOINT ["java", "-jar", "/app/app.jar"]
