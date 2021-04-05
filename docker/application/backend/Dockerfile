FROM {{ images-registry }}/openjdk:8-jre-slim
EXPOSE 8080
RUN mkdir -p /dbo
WORKDIR /dbo
COPY dbo-1.0-SNAPSHOT.jar /dbo
ENTRYPOINT ["java", "-jar", "dbo-1.0-SNAPSHOT.jar"]
