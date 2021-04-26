FROM {{ soft-registry }}/openjdk:8-jre-slim
EXPOSE 8080
RUN mkdir -p /dbo
WORKDIR /dbo
COPY dbo-1.0-SNAPSHOT.jar .
ENTRYPOINT ["java", "-jar", "dbo-1.0-SNAPSHOT.jar"]
CMD ["--spring.profiles.active=qa"]
