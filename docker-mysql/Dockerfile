FROM centos

MAINTAINER tonywell <tongwei1985@gmail.com>

# root password
RUN echo 'root:!23$QweR' | chpasswd

# 为解决Header V3 RSA/SHA256 Signature, key ID f4a80eb5: NOKEY问题
RUN rpm --import /etc/pki/rpm-gpg/RPM*

RUN \
    yum -y install \
        openssh openssh-server openssh-clients gcc gcc-c++ make autoconf bison ncurses-devel which \
        sudo passwd wget &&\
        yum clean all

# 设置sshd
RUN sshd-keygen
RUN sed -i "s/#UsePrivilegeSeparation.*/UsePrivilegeSeparation no/g" /etc/ssh/sshd_config
RUN sed -i "s/UsePAM.*/UsePAM no/g" /etc/ssh/sshd_config

RUN mkdir /var/run/sshd

RUN mkdir /opt/software

#解决Could not find (the correct version of) boost 问题
#RUN wget http://120.52.72.22/nchc.dl.sourceforge.net/c3pr90ntc0td/project/boost/boost/1.59.0/boost_1_59_0.tar.gz && \
#    tar -zvxf boost_1_59_0.tar.gz && cd boost_1_59_0 && \
#    ./bootstrap.sh && \
#    ./b2 stage threading=multi link=shared && \
#    ./b2 install threading=multi link=shared && \
#    cd ../ && rm -rf boost_1_59_0*

RUN wget http://www.cmake.org/files/v3.0/cmake-3.0.1.tar.gz && \
    tar zxvf cmake-3.0.1.tar.gz && \
    cd cmake-3.0.1 && ./configure --prefix=/usr/local/cmake && gmake && \
    make && make install && \
    cd ../ && rm -rf cmake-3.0.1*



#源码安装mysql
RUN wget ftp://temp:temp@192.168.50.104/centos7/mysql-5.6.29.tar.gz && \
     tar -zxvf mysql-5.6.29.tar.gz -C /opt/software/ && \
     rm mysql-5.6.29.tar.gz && \
     cd /opt/software/mysql-5.6.29 && \
     /usr/local/cmake/bin/cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/mysql-5.6.29 -DMYSQL_DATADIR=/usr/local/mysql-5.6.29/data -DSYSCONFDIR=/etc -DWITH_INNOBASE_STORAGE_ENGINE=1 -DWITH_ARCHIVE_STORAGE_ENGINE=1 -DWITH_BLACKHOLE_STORAGE_ENGINE=1 -DWITH_PARTITION_STORAGE_ENGINE=1 -DWITH_PERFSCHEMA_STORAGE_ENGINE=1 -DWITHOUT_EXAMPLE_STORAGE_ENGINE=1 -DWITHOUT_FEDERATED_STORAGE_ENGINE=1 -DDEFAULT_CHARSET=utf8 -DDEFAULT_COLLATION=utf8_general_ci -DWITH_EXTRA_CHARSETS=all -DENABLED_LOCAL_INFILE=1 -DWITH_READLINE=1 -DMYSQL_UNIX_ADDR=/usr/local/mysql-5.6.29/mysql.sock -DMYSQL_TCP_PORT=3306 -DMYSQL_USER=mysql -DCOMPILATION_COMMENT="lq-edition" -DENABLE_DTRACE=0 -DOPTIMIZER_TRACE=1 -DWITH_DEBUG=1 && \
     make && make install

# 添加测试用户mysql，密码mysql，并且将此用户添加到sudoers里
RUN useradd mysql
RUN echo "mysql:mysql" | chpasswd
RUN echo "mysql   ALL=(ALL)       ALL" >> /etc/sudoers

RUN cd /usr/local/mysql-5.6.29 && chown -R mysql:mysql ./

COPY my.cnf /etc/my.cnf
RUN chown mysql:mysql /etc/my.cnf

RUN cd /usr/local/mysql-5.6.29 && ./scripts/mysql_install_db --user=mysql --basedir=/usr/local/mysql-5.6.29 --datadir=/usr/local/mysql-5.6.29/data/

ENV MYSQL_HOME /usr/local/mysql-5.6.29

# 容器需要开放MySQL 3306端口
EXPOSE 3306

CMD ["/usr/sbin/sshd", "-D"]

