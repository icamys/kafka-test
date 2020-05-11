FROM ubuntu:18.04
RUN apt-get update \
    && apt-get install -y unzip openjdk-11-jre-headless wget supervisor docker.io openssh-server gnupg2
ENV JAVA_HOME /usr/lib/jvm/java-11-openjdk-amd64/
RUN echo 'root:root' | chpasswd
RUN mkdir /var/run/sshd
RUN sed -i 's/PermitRootLogin without-password/PermitRootLogin yes/' /etc/ssh/sshd_config

EXPOSE 22

ENV ZOOKEEPER_VERSION 3.6.1

#Download Zookeeper
RUN wget -q http://mirror.vorboss.net/apache/zookeeper/zookeeper-${ZOOKEEPER_VERSION}/apache-zookeeper-${ZOOKEEPER_VERSION}-bin.tar.gz && \
wget -q https://www.apache.org/dist/zookeeper/KEYS && \
wget -q https://www.apache.org/dist/zookeeper/zookeeper-${ZOOKEEPER_VERSION}/apache-zookeeper-${ZOOKEEPER_VERSION}-bin.tar.gz.asc && \
wget -q https://www.apache.org/dist/zookeeper/zookeeper-${ZOOKEEPER_VERSION}/apache-zookeeper-${ZOOKEEPER_VERSION}-bin.tar.gz.sha512

#Verify download
RUN sha512sum -c apache-zookeeper-${ZOOKEEPER_VERSION}-bin.tar.gz.sha512 && \
gpg --import KEYS && \
gpg --verify apache-zookeeper-${ZOOKEEPER_VERSION}-bin.tar.gz.asc

#Install
RUN tar -xzf apache-zookeeper-${ZOOKEEPER_VERSION}-bin.tar.gz -C /opt

#Configure
RUN mv /opt/apache-zookeeper-${ZOOKEEPER_VERSION}-bin/conf/zoo_sample.cfg /opt/apache-zookeeper-${ZOOKEEPER_VERSION}-bin/conf/zoo.cfg

ENV JAVA_HOME /usr/lib/jvm/java-7-openjdk-amd64
ENV ZK_HOME /opt/apache-zookeeper-${ZOOKEEPER_VERSION}-bin
RUN sed  -i "s|/tmp/zookeeper|$ZK_HOME/data|g" $ZK_HOME/conf/zoo.cfg; mkdir $ZK_HOME/data

ADD start-zk.sh /usr/bin/start-zk.sh
EXPOSE 2181 2888 3888

WORKDIR /opt/apache-zookeeper-${ZOOKEEPER_VERSION}-bin
VOLUME ["/opt/apache-zookeeper-${ZOOKEEPER_VERSION}-bin/conf", "/opt/apache-zookeeper-${ZOOKEEPER_VERSION}-bin/data"]

CMD /usr/sbin/sshd && bash /usr/bin/start-zk.sh