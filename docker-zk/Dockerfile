FROM tonywell/centos-java

RUN wget http://apache.fayea.com/zookeeper/zookeeper-3.4.6/zookeeper-3.4.6.tar.gz && \
      tar -zvxf zookeeper-3.4.6.tar.gz -C /opt/ && \
      mv /opt/zookeeper-3.4.6 /opt/zookeeper && \
      rm zookeeper-3.4.6.tar.gz

RUN mkdir -p /opt/data
RUN mkdir -p /opt/log

ENV ZOO_HOME /opt/zookeeper
ENV PATH $PATH:$ZOO_HOME/bin

ENV TZ "Asia/Shanghai"
EXPOSE 2181 2888 3888
CMD ["zkServer.sh", "start-foreground"]
