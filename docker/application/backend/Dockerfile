FROM artifactory.raiffeisen.ru/ext-rbru-techimage-docker/openjdk:8-jre-slim
EXPOSE 8080
RUN mkdir -p /dbo
WORKDIR /dbo
ENTRYPOINT ["java", "-jar", "dbo-1.0-SNAPSHOT.jar"]
COPY dbo-1.0-SNAPSHOT.jar /dbo
