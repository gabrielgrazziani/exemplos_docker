FROM ubuntu as install
ENV atualizacao = "26/11/2020"

WORKDIR /workspace/app
RUN apt-get update; apt-get -y install git
RUN git clone https://github.com/spring-guides/gs-spring-boot-docker.git

FROM maven:latest as build
WORKDIR /workspace/app

ARG GIT=/workspace/app
COPY --from=install ${GIT}/gs-spring-boot-docker/ .
WORKDIR /workspace/app/initial/

RUN mvn clean package -Dmaven.test.skip -Dmaven.main.skip -Dspring-boot.repackage.skip && rm -r target/

COPY ./Application.java src/main/java/hello/
RUN mvn package  -Dmaven.test.skip
RUN mkdir -p target/dependency && (cd target/dependency; jar -xf ../*.jar)

FROM openjdk:8-jdk-alpine
ARG DEPENDENCY=/workspace/app/initial/target/dependency
COPY --from=build ${DEPENDENCY}/BOOT-INF/lib /app/lib
COPY --from=build ${DEPENDENCY}/META-INF /app/META-INF
COPY --from=build ${DEPENDENCY}/BOOT-INF/classes /app
ENTRYPOINT ["java","-cp","app:app/lib/*","hello.Application"]