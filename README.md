# Java CI/CD Pipeline with Jenkins & Docker

This project demonstrates a complete CI/CD pipeline setup using **Jenkins**, **Docker**, and **Slack** for a Java application.

## 🔧 Technologies Used
- Java (Gradle or Maven)
- Jenkins (Declarative Pipeline)
- Docker & DockerHub
- Trivy (Image Scanning)
- Slack (Notification Integration)
- Git & GitHub
- AWS EC2 (for hosting Jenkins & workers)

## 📂 Pipeline Stages
1. `Clean Workspace`
2. `Checkout Code`
3. `Code Quality Analysis` (with SonarQube)
4. `Build`
5. `Archive Artifact`
6. `Create Docker Image`
7. `Scan Docker Image`
8. `Push Image to DockerHub`
9. `Deploy Stack` (using Docker Swarm)

## 📦 Jenkinsfile
See the full pipeline script in [Jenkinsfile](./Jenkinsfile)

## 🐳 Dockerfile

Docker-app -->Multistage -->dockerfile::::

FROM openjdk:8 AS BUILD_IMAGE
RUN apt update && apt install maven -y
RUN git clone -b vp-docker https://github.com/imranvisualpath/vprofile-repo.git
RUN cd vprofile-repo && mvn install

FROM tomcat:8-jre11

RUN rm -rf /usr/local/tomcat/webapps/*

COPY --from=BUILD_IMAGE vprofile-repo/target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war

EXPOSE 8080
CMD ["catalina.sh", "run"]

Docker-app -->-->dockerfile::::


FROM tomcat:8-jre11

RUN rm -rf /usr/local/tomcat/webapps/*

COPY target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war

EXPOSE 8080
CMD ["catalina.sh", "run"]


Docker-db -->-->dockerfile::::

FROM mysql:5.7.25

ENV MYSQL_ROOT_PASSWORD="devopspassword"
ENV MYSQL_DATABASE="accounts"

ADD db_backup.sql docker-entrypoint-initdb.d/db_backup.sql


📸 Slack Integration
Slack is integrated to post pipeline status updates to a channel for real-time monitoring.

DEPLOYEMENT::::
      [docker stack deploy -c docker-compose.yml mystack]



