# Build
FROM maven:3.8.6-amazoncorretto-17 AS myapp-build2
ENV MYAPP_HOME /opt/myapp
WORKDIR $MYAPP_HOME
COPY pom.xml .
COPY src ./src
RUN mvn package -DskipTests

# Run
FROM amazoncorretto:17
ENV MYAPP_HOME /opt/myapp
WORKDIR $MYAPP_HOME
COPY --from=myapp-build2 $MYAPP_HOME/target/*.jar $MYAPP_HOME/myapp2.jar

ENTRYPOINT java -jar myapp2.jar