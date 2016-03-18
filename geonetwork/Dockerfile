#
# Dockerfile for the geonetwork service
#
# run this one with --privileged to overcome https://github.com/docker/docker/issues/6800
# (docker run -it --cap-add SYS_PTRACE does not fix it)

FROM debian:jessie

MAINTAINER PSC "francois.vanderbiest@camptocamp.com"

ENV DEBIAN_FRONTEND noninterative

RUN apt-get update && \
    apt-get install -y tomcat8 unzip && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN rm -rf /var/lib/tomcat8/webapps/*

ADD http://build.georchestra.org/geonetwork-3.0.1-SNAPSHOT.war /var/lib/tomcat8/webapps/geonetwork.war

RUN mkdir -p /var/lib/tomcat8/webapps/geonetwork && \
    unzip -e /var/lib/tomcat8/webapps/geonetwork.war -d /var/lib/tomcat8/webapps/geonetwork && \
    chown -R tomcat8:tomcat8 /var/lib/tomcat8/webapps/geonetwork

# replace the webapp's own config
COPY srv.xml /var/lib/tomcat8/webapps/geonetwork/WEB-INF/config-node/srv.xml
COPY jdbc.properties /var/lib/tomcat8/webapps/geonetwork/WEB-INF/config-db/jdbc.properties

RUN mkdir /opt/geonetwork_data_dir
RUN chown -R tomcat8 /opt/geonetwork_data_dir

RUN echo "JAVA_OPTS=\"\${JAVA_OPTS} -Dgeonetwork.dir=/opt/geonetwork_data_dir \"" >> /etc/default/tomcat8
RUN echo "JAVA_OPTS=\"\${JAVA_OPTS} -Dfile.encoding=UTF-8 \"" >> /etc/default/tomcat8
RUN echo "JAVA_OPTS=\"\${JAVA_OPTS} -Xms128m -Xmx712m -XX:PermSize=128m -XX:MaxPermSize=128m \"" >> /etc/default/tomcat8
# workaround for https://github.com/georchestra/georchestra/issues/871
RUN echo "JAVA_OPTS=\"\${JAVA_OPTS} -Djava.util.prefs.userRoot=/tmp -Djava.util.prefs.systemRoot=/tmp \"" >> /etc/default/tomcat8

# Expose the port where tomcat is listening
EXPOSE 8080

VOLUME ["/tmp"] # server logs

# Start Tomcat server
# The last line (the CMD command) is used to make a fake always-running
# command (the tail command); thus, the Docker container will keep running.
CMD /etc/init.d/tomcat8 start && tail -F /var/log/tomcat8/catalina.out
