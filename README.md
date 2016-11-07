# Bigdata-Docker构建大数据学习开发环境


### 介绍

##### 1、镜像环境

* 系统：centos 7
* Java ：java7
* Zookeeper: 3.4.6
* Hadoop: 2.7.1
* mysql: 5.6.29
* Hive: 1.2.1
* Spark: 1.6.2
* Hbase: 1.1.2

##### 2、镜像介绍

* tonywell/centos-java：openssh、java7，基础镜像
* tonywell/docker-zk:  基于tonywell/centos-java构建，zookeeper，用于启动zk集群
* tonywell/docker-hadoop：基于tonywell/centos-java构建, hadoop，用于启动hadoop集群
* tonywell/docker-mysql：openssh、mysql，用于启动mysql容器提供给hive集群
* tonywell/docker-hive：基于tonywell/docker-hadoop镜像构建，包含hadoop、hive，用于启动hadoop、hive集群
* tonywell/docker-spark：基于tonywell/docker-hive镜像构建，包含hadoop、hive、spark，用于启动hadoo、hive、spark集群
* tonywell/docker-hbase：基于tonywell/docker-spark镜像构建，包含hadoop、hive、spark、hbase，用于启动hadoop、hive、spark、hbase集群



### Quick Start

#### 1、构建镜像

```
$ sh build.sh
```

可以根据需求注释掉不需要的镜像

#### 2、创建大数据集群网络

```
$ docker network create zoo
```

#### 3、启动zk集群

```
$ docker-compose -f docker-compose-zk.yml up -d
```

根据需要可在compose膜拜中增减集群数量，注意同时要增减myid配置

#### 4、启动mysql容器

如何仅仅想使用hadoop集群的，可省略此步。

```
$ docker-compose -f docker-compose-mysql.yml up -d
```

然后就要修改密码和配置远程访问mysql了

```
$ docker exec -it hadoop-mysql bash
$ cd /usr/local/mysql-5.6.29/bin
$ ./mysql -u root -p
#默认密码为空，回车即可
$ mysql> use mysql;
$ mysql> UPDATE user SET Password=PASSWORD('新密码') where USER='root';
$ mysql> FLUSH PRIVILEGES;
#授权远程访问
$ mysql> grant ALL PRIVILEGES ON *.* to root@"%" identified by "root" WITH GRANT OPTION;
$ mysql> FLUSH PRIVILEGES;
#配置字符集，解决后面hive建表报错
#FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask. MetaException(message:For direct MetaStore DB connections, we don't support retries at the client level.)
$ mysql> alter database hive character set latin1;
```

ok mysql容器配置完成

#### 4、大数据集群

##### a）启动Hadoop集群

```
$ docker-compose -f docker-compose-hadoop.yml up -d
```

启动集群，格式化namenode

```
$ docker exec -it hadoop-master bash
$ cd /usr/local/hadoop/bin
$ hdfs namenode -format
```

然后启动hdfs和yarn

```
$ cd /usr/local/hadoop/sbin
$ ./start-all.sh
```

 访问http://localhost:50070，看集群是否启动成功

##### b）启动Hive集群

需要依赖mysql容器

```
$ docker-compose -f docker-compose-hive.yml up -d
```

 启动hadoo集群的操作和上面启动hadoop集群一样

##### c）启动Spark集群

需要依赖mysql容器

```
$ docker-compose -f docker-compose-spark.yml up -d
```

 启动hadoop集群同a。

启动spark集群

```
$ sh /usr/local/spark/sbin/start-all.sh
```

使用 spark 自带样例中的计算 Pi 的应用来验证一下

```
/usr/local/spark/bin/spark-submit --master spark://hadoop-master:7077 --class org.apache.spark.examples.SparkPi /usr/local/spark/lib/spark-examples-1.6.2-hadoop2.2.0.jar 1000
```

计算结果输出如下

```
starting org.apache.spark.deploy.master.Master, logging to /usr/local/spark/logs/spark--org.apache.spark.deploy.master.Master-1-1bdfd98bccc7.out
hadoop-slave2: starting org.apache.spark.deploy.worker.Worker, logging to /usr/local/spark/logs/spark-root-org.apache.spark.deploy.worker.Worker-1-9dd7e2ebbf13.out
hadoop-slave3: starting org.apache.spark.deploy.worker.Worker, logging to /usr/local/spark/logs/spark-root-org.apache.spark.deploy.worker.Worker-1-97a87730dd03.out
hadoop-slave1: starting org.apache.spark.deploy.worker.Worker, logging to /usr/local/spark/logs/spark-root-org.apache.spark.deploy.worker.Worker-1-adb07707f15b.out
<k/bin/spark-submit --master spark://hadoop-master:7077 --class org.apache.spark.examples.SparkPi /usr/local/spark/li
lib/      licenses/
<.examples.SparkPi /usr/local/spark/lib/spark-examples-1.6.2-hadoop2.2.0.jar 1000
16/11/07 08:19:46 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
Pi is roughly 3.1417756
```



##### d）启动Hbase集群

```
$ docker-compose -f docker-compose-hbase.yml up -d
```

启动hadoop、spark集群同c

启动hbase集群

```
$ sh /usr/local/hbase/bin/start-hbase.sh
```





注意docker-compose-hadoop.yml、docker-compose-hive.yml、docker-compose-spark.yml和docker-compose-hbase.yml不要一起启动，后面模板中是包含了前一个的所有配置