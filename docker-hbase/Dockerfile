FROM tonywell/docker-spark

MAINTAINER tonywell <tongwei1985@gmail.com>

ENV HBASE_HOME=/usr/local/hbase
ENV PATH=$PATH:$HBASE_HOME/bin

RUN wget http://archive.apache.org/dist/hbase/1.1.2/hbase-1.1.2-bin.tar.gz && \
	tar -xzvf hbase-1.1.2-bin.tar.gz -C /usr/local/ && \
        mv /usr/local/hbase-1.1.2 $HBASE_HOME && \
	rm -rf hbase-1.1.2-bin.tar.gz

