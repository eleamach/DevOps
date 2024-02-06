# TP 1

Docker

## Installation
### Vanilla
#### Database
Building the image :

```bash
$ sudo docker build -t premierexercice .
```  

Running the image : 
```bash
$ sudo docker run -v /tmp/DevOps/data:/var/lib/postgresql/data -e POSTGRES_USER=usr -e POSTGRES_PASSWORD=pwd -d --name bd --network app-network premierexercice
```     
<br>

#### Adminer
Running adminer : 
```bash
$ sudo docker ps
CONTAINER ID   IMAGE             COMMAND                  CREATED          STATUS          PORTS      NAMES
b23d5993a760   premierexercice   "docker-entrypoint.s…"   37 seconds ago   Up 36 seconds   5432/tcp   bd

```
```bash
$ sudo docker run -d --link suspicious_mirzakhani:db -p 8083:8080 adminer
```

<br>

#### Network
Create network : 
```bash
$ sudo docker network create app-network
```

<br>

#### Back
Building the image java :

```bash
$ sudo docker build -t exodeux .
```

Run the image java :

```bash
$ sudo docker run -it --rm exodeux
```

<br>

#### Simple API

Building the image for simpleapi : 
```bash
$ sudo docker build -t simpleapi .
```

Runing the image for simpleapi : 
```bash
$ sudo docker run -it --rm -p 8081:8080 simpleapi 
```

<br>

#### Student api

Building the image for studentsimpleapi : 
```bash
$ sudo docker build -t simple-api-student .
```

Running the image for studentsimpleapi : 
```bash
$ sudo docker run -dit --rm --name springapi -p 8080:8080  --network app-network simple-api-student 
```

<br>

#### HTTP Server
Building the image for http : 
```bash
$ sudo docker build -t my-apache2 .
```

Runing the image for http : 
```bash
$ sudo docker run -dit --name my-running-app --network app-network -p 80:80 my-apache2
```

<br>
<br>

## Docker compose
Build : 
```bash
$ sudo docker compose build
```
<br>

Run : 
```bash
$ sudo docker compose up
```

<br>

## Questions
**Why do we need a multistage build?** 
- Useful to optimize Dockerfiles
- Here it is important if we don't want to have a lot of Dockerfile going on
- Structurate your docker

<br>

**And explain each step of this dockerfile.**
```bash
# Build
FROM maven:3.8.6-amazoncorretto-17 AS myapp-build
ENV MYAPP_HOME /opt/myapp
WORKDIR $MYAPP_HOME
COPY pom.xml .
COPY src ./src
RUN mvn package -DskipTests

# Run
FROM amazoncorretto:17
ENV MYAPP_HOME /opt/myapp
WORKDIR $MYAPP_HOME
COPY --from=myapp-build $MYAPP_HOME/target/*.jar $MYAPP_HOME/myapp.jar

ENTRYPOINT java -jar myapp.jar
```
 - FROM ... AS ... we are here naming the stages so that we can reuse them later on
 - MYAPP_HOME is an env pointing to /opt:myapp
 - It became our working directory
 - Then it copy pom.xml, src
 - run "mvn package -DskipTests"
 - Copy onto a jar
 - And finally, configure a container that will run as an executable


<br>

**Why do we need a reverse proxy**
- Prevents malicious person from directly targeting your origin server using its IP address
- Any communication coming from the outside has to go through the reverse proxy first


<br>

**Why is docker-compose so important?**
- Running multi-container applications
- Efficient development and deployment
- Easy to manage services, networks, and volumes in a single, comprehensible YAML configuration file

<br>

**1-3 Document docker-compose most important commands**
1. ```$ sudo docker-compose build``` build images
2. ```$ sudo docker-compose up``` start containers
3. ```$ sudo docker-compose down``` stop containers/networks/volumes
4. ```$ sudo docker-compose start``` start containers
5. ```$ sudo docker-compose stop``` stop containers
6. ```$ sudo docker-compose restart``` restart container
<br>

**1-4 Document your docker-compose file.**
```bash
version: '3.8'

services:
    backend:
        container_name: backend #Name we want to give to the container
        build: ./Back/simpleapi/simple-api-student-main #PAth of the Dockerfile 
        networks:
         - app-network #Network used to connect back/db/httpd
         
        depends_on: 
         - database #Can only be up if database is running
        

    database:
        container_name: database #Name we want to give to the container
        build: ./Postgre #PAth of the Dockerfile 
        networks:
         - app-network #Network used to connect back/db/httpd
        volumes:
         - /tmp/DevOps/bd:/var/lib/postgresql/data #Indicate where the volume is located

    httpd:
        container_name: httpd #Name we want to give to the container
        build: ./httpserver  #PAth of the Dockerfile 
        ports: 
         - 80:80 #Port used
        networks:
         - app-network #Network used to connect back/db/httpd
        depends_on: 
         - backend #Can only be up if database is running

networks:
    app-network: 
```
<br>

**1-5 Document your publication commands and published images in dockerhub.**
Connection do dockerhub :
```bash
$ sudo  docker login
```
<br>

Tag images :
```bash
$ sudo  docker ps
CONTAINER ID   IMAGE             COMMAND                  CREATED          STATUS          PORTS                               NAMES
a787321aff14   devops-httpd      "httpd-foreground"       48 minutes ago   Up 48 minutes   0.0.0.0:80->80/tcp, :::80->80/tcp   httpd
e18fea04e787   devops-backend    "/bin/sh -c 'java -j…"   48 minutes ago   Up 48 minutes                                       backend
a7694d6d0b98   devops-database   "docker-entrypoint.s…"   48 minutes ago   Up 48 minutes   5432/tcp                            database
```
```bash
$ sudo  docker tag devops-backend USERNAME/backend:1.0    
$ sudo  docker tag devops-httpd USERNAME/httpd:1.0
$ sudo  docker tag devops-database USERNAME/db:1.0
``` 
With your username

<br>

Publish images :
```bash
$ sudo  docker push eleamct/db:1.0
$ sudo  docker push eleamct/backend:1.0
$ sudo  docker push eleamct/httpd:1.0
```
<br>

**Why do we put our images into an online repo?**
- Accessibility from anywhere
- Collaboration
- Versioning

<br>
<br>
<br>




# TP 2
Github

## Installation
Building and running tests : 
```bash
mvn clean verify
```
-> it will clear previous builds inside cache and freshly build and run tests.

<br>

## Questions

**2-1 What are testcontainers?**
They simply are java libraries that allow you to run a bunch of docker containers while testing. 