FROM maven:3.6-jdk-8 AS builder
WORKDIR /app

RUN git clone -b v1.9.2 https://gitee.com/xuxueli0323/xxl-job.git xxl-job-1.9.2 && \
    cd xxl-job-1.9.2 && \
    mvn clean package -Dmaven.test.skip=true

FROM tomcat:8.5-jdk8

RUN rm -rf /usr/local/tomcat/webapps/* && mkdir /usr/local/tomcat/webapps/ROOT

COPY --from=builder /app/xxl-job-1.9.2/xxl-job-admin/target/xxl-job-admin-1.9.2.war /tmp/ROOT.war

RUN cd /usr/local/tomcat/webapps/ROOT && jar -xf /tmp/ROOT.war && rm /tmp/ROOT.war

EXPOSE 8080
CMD ["catalina.sh", "run"]
