FROM maven:3.9-eclipse-temurin-21 AS build
WORKDIR /app
COPY pom.xml .
COPY services/common/pom.xml services/common/pom.xml
COPY services/order/pom.xml services/order/pom.xml
RUN mvn -B -f pom.xml install -N
COPY services/common/src services/common/src
RUN mvn -B -f services/common/pom.xml install -DskipTests
RUN mvn -B -f services/order/pom.xml dependency:go-offline
COPY services/order/src services/order/src
RUN mvn -B -f services/order/pom.xml clean package -DskipTests

FROM eclipse-temurin:21-jre
WORKDIR /app
COPY --from=build /app/services/order/target/*.jar app.jar
EXPOSE 8084
ENTRYPOINT ["java", "-jar", "/app/app.jar"]
