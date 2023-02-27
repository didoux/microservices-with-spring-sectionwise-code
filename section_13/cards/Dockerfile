#Start with a base image containing Maven & Java runtime
FROM maven:3.9.0-eclipse-temurin-17 AS build

#Information around who maintains the image
MAINTAINER Dan Idoux

VOLUME /tmp
ENV HOME=/home/app
COPY src /home/app/src
COPY pom.xml $HOME
RUN mkdir -p /root/.m2 \
    && mkdir /root/.m2/repository
# Used for git-commit-id-maven-plugin
# COPY .git/ $HOME/.git/

RUN mvn -f $HOME/pom.xml verify -DskipTests
# Subsequent runs will use local dependencies and execute much faster. (https://www.baeldung.com/ops/docker-cache-maven-dependencies)
RUN mvn -f /home/app/pom.xml clean package -DskipTests

#
# Package stage
#
FROM eclipse-temurin:17.0.6_10-jre
VOLUME /tmp
RUN groupadd -r -g 2000 easybank && useradd -m -d /home/easybank -u 2000 -r -g easybank easybank

# Add the application's jar to the container
COPY --from=build /home/app/target/cards-0.0.1-SNAPSHOT.jar /usr/local/lib/cards.jar
USER easybank
EXPOSE 9000

#execute the application
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/usr/local/lib/cards.jar"]
