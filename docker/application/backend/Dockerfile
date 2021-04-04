#FROM adoptopenjdk/openjdk:8-jre-slim
FROM adoptopenjdk/openjdk8:x86_64-alpine-jdk8u282-b08-slim
EXPOSE 8080
RUN mkdir -p /dbo
WORKDIR /dbo
ENTRYPOINT ["java", "-jar", "dbo-1.0-SNAPSHOT.jar"]
COPY dbo-1.0-SNAPSHOT.jar /dbo
