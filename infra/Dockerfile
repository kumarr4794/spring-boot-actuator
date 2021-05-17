FROM openjdk:8-jdk-alpine
RUN apk add --no-cache --upgrade bash
RUN apk add --no-cache --upgrade curl
EXPOSE 9090
COPY ./spring-boot-actuator-0.0.1-SNAPSHOT.jar app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
