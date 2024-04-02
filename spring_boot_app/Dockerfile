#It uses a lightweight Alpine Linux-based image with OpenJDK 11 provided by AdoptOpenJDK.
FROM adoptopenjdk/openjdk11:alpine-jre

#This line declares a build argument named artifact, which specifies the path to the artifact (JAR file) of the Spring Boot application.
ARG artifact=target/spring-boot-web.jar

#This line sets the working directory inside the Docker container to /opt/app. All subsequent commands will be executed relative to this directory.
WORKDIR /opt/app 

#it will copy the JAR file located at target/spring-boot-web.jar into the container as app.jar.
COPY ${artifact} app.jar 

# This should not be changed
ENTRYPOINT [ "java","-jar","app.jar" ]