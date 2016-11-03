FROM centos
MAINTAINER tonywell <tongwei1985@gmail.com>

# root password
RUN echo 'root:!23$QweR' | chpasswd

# 为解决Header V3 RSA/SHA256 Signature, key ID f4a80eb5: NOKEY问题
RUN rpm --import /etc/pki/rpm-gpg/RPM*

RUN \
    yum -y install \
        openssh openssh-server openssh-clients \
        sudo passwd wget &&\
        yum clean all

RUN ssh-keygen -q -N "" -t dsa -f /etc/ssh/ssh_host_dsa_key
RUN ssh-keygen -q -N "" -t rsa -f /etc/ssh/ssh_host_rsa_key
RUN ssh-keygen -q -N "" -t rsa -f /root/.ssh/id_rsa
RUN cp /root/.ssh/id_rsa.pub /root/.ssh/authorized_keys

# 设置sshd
RUN sshd-keygen
RUN sed -i "s/#UsePrivilegeSeparation.*/UsePrivilegeSeparation no/g" /etc/ssh/sshd_config
RUN sed -i "s/UsePAM.*/UsePAM no/g" /etc/ssh/sshd_config

RUN mkdir /var/run/sshd

RUN wget http://119.254.110.32:8081/download/jdk1.7.0_60.tar.gz && \
       tar -zxvf jdk1.7.0_60.tar.gz -C /usr/local/ && \
       rm -rf jdk1.7.0_60.tar.gz

#ADD ./jdk1.7.0_60.tar.gz /usr/local/

RUN mv /usr/local/jdk1.7.0_60 /usr/local/jdk1.7

ENV JAVA_HOME /usr/local/jdk1.7
ENV PATH $JAVA_HOME/bin:$PATH

EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]
