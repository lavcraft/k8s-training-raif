#FROM adoptopenjdk/openjdk:8-jre-slim
FROM adoptopenjdk/openjdk8:x86_64-alpine-jdk8u282-b08-slim
EXPOSE 8888
RUN mkdir -p /stub/mappings
WORKDIR /stub
COPY wiremock-jre8-standalone-2.27.1.jar /stub
ENTRYPOINT ["java", "-jar", "wiremock-jre8-standalone-2.27.1.jar", "--verbose", "--port", "8888"]
COPY mappings/* mappings/
