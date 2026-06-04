FROM openjdk:8-jdk-alpine
#FROM eclipse-temurin:8-jre

EXPOSE 8080

RUN mkdir /opt/bootcamp-java-mysql-app
RUN addgroup -S mygroup && adduser -S myuser -G mygroup
RUN chown -R myuser:mygroup /opt/bootcamp-java-mysql-app
USER myuser

COPY ./build/libs/bootcamp-java-mysql-project-1.2-SNAPSHOT.jar /opt/bootcamp-java-mysql-app

CMD ["java", "-jar", "/opt/bootcamp-java-mysql-app/bootcamp-java-mysql-project-1.2-SNAPSHOT.jar"]

