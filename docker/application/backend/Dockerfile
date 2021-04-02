FROM adoptopenjdk/openjdk8:alpine-jre
EXPOSE 8080
RUN mkdir -p /dbo
WORKDIR /dbo
ENTRYPOINT ["java", "-jar", "dbo-1.0-SNAPSHOT.jar"]
COPY dbo-1.0-SNAPSHOT.jar /dbo
