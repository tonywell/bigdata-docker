FROM tonywell/docker-hive

MAINTAINER tonywell <tongwei1985@gmail.com>

ENV SPARK_HOME=/usr/local/spark
ENV PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin:.

ENV JAVA_HOME /usr/local/jdk1.7
ENV PATH $JAVA_HOME/bin:$PATH

RUN wget http://apache.fayea.com/spark/spark-1.6.2/spark-1.6.2-bin-without-hadoop.tgz && \
    tar -xzvf spark-1.6.2-bin-without-hadoop.tgz -C /usr/local/ && \
    mv /usr/local/spark-1.6.2-bin-without-hadoop /usr/local/spark && \
    rm -rf spark-1.6.2-bin-without-hadoop.tgz 
