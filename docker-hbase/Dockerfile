FROM tonywell/docker-spark

MAINTAINER tonywell <tongwei1985@gmail.com>

ENV HBASE_HOME=/usr/local/hbase
ENV PATH=$PATH:$HBASE_HOME/bin

#RUN wget http://archive.apache.org/dist/hbase/1.1.2/hbase-1.1.2-bin.tar.gz && \
RUN wget ftp://temp:temp@192.168.50.104/centos7/hbase/hbase-1.1.2-bin.tar.gz && \
	tar -xzvf hbase-1.1.2-bin.tar.gz -C /usr/local/ && \
        mv /usr/local/hbase-1.1.2 $HBASE_HOME && \
	rm -rf hbase-1.1.2-bin.tar.gz

# Hdfs ports
EXPOSE 9000 50010 50020 50070 50075 50090
# See https://issues.apache.org/jira/browse/HDFS-9427
EXPOSE 9871 9870 9820 9869 9868 9867 9866 9865 9864
# Mapred ports
EXPOSE 19888
#Yarn ports
EXPOSE 8030 8031 8032 8033 8040 8042 8088 8188
#Other ports
EXPOSE 49707 2122
