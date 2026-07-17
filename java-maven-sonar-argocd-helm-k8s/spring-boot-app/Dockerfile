FROM adoptopenjdk/openjdk11:alpine-jre
ARG artifact=target/spring-boot-web.jar
WORKDIR /app
COPY ${artifact} app.jar
ENTRYPOINT ["java","-jar", "app.jar"]

