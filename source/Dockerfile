FROM node:alpine3.10 AS npm
RUN apk add python
RUN apk add make
RUN apk add g++
#RUN npm install -g cnpm --registry=https://registry.npm.taobao.org
RUN npm config set registry https://registry.npm.taobao.org
WORKDIR /build
COPY vue .
WORKDIR /build/xzs-admin
RUN yarn && npm run build
WORKDIR /build/xzs-student
RUN yarn && npm run build

RUN ls -l /build/xzs-admin

FROM maven:3.6.3-jdk-8 AS jdk

WORKDIR /build
# Let's cache modules retrieval - those don't change so often
COPY xzs .
RUN mvn compile 
COPY --from=npm /build/xzs-admin/admin  src/main/resources/static
COPY --from=npm /build/xzs-student/student  src/main/resources/static
RUN mvn package 
RUN ls -l /build/target
# Copy the code necessary to build the application
# You may want to change this to copy only what you actually need.



# Create the minimal runtime image
FROM openjdk:8

ENTRYPOINT ["/usr/local/openjdk-8/bin/java", "-jar", "/app/xzs-3.2.0.jar"]
RUN echo "Asia/Shanghai" > /etc/timezone
# Add Maven dependencies (not shaded into the artifact; Docker-cached)
# Add the service itself
#ARG JAR_FILE
#ADD target/${JAR_FILE} /usr/share/rms/app.jar


COPY --from=jdk /build/target/xzs-3.2.0.jar /app/xzs-3.2.0.jar
#ENTRYPOINT ["/hpor"]


