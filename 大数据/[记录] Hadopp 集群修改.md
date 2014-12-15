#Hadoop 集群修改记录

[TOC]

##修改说明

原有的Master63集群，采用的不同版本的JAVA、Hadoop、HBase、Hive、Sqoop，现在需要调整到指定版本。

具体的目标版本信息如下：

|工具|版本|
|---|---|
|JAVA     |     jdk1.7.0_65
|Hadoop  |   hadoop-2.5.0.tar.gz
|HBase  |      hbase-0.98.6.1-hadoop2-bin.tar.gz
|Hive  |         apache-hive-0.13.1-bin
|Sqoop|       sqoop-1.4.5


##详细步骤

>提示：
在进行修改操作前，先停掉所有的hadoop的服务！

###1. JDK修改

下载`jdk1.7.0_65.tar.gz`，放到集群中每个机器的`/usr/local`路径下。

>如果已经有JDK，可以直接使用已有的版本。
//压缩 jdk1.7.0_65文件夹 为 jdk1.7.0_65.tar.gz
`# tar -czvf jdk1.7.0_65.tar.gz    jdk1.7.0_65`

在该目录下，解压文件
```bash
$ tar zxvf jdk1.7.0_65.tar.gz 
```

修改`/etc/profile`配置文件：
```
#JDK Setting
JAVA_HOME=/usr/local/jdk1.7.0_65
JAVA_BIN=/usr/local/jdk1.7.0_65/bin
PATH=$PATH:$JAVA_BIN
CLASSPATH=$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:./
export JAVA_HOME JAVA_BIN PATH CLASSPATH
```

执行`source /etc/profile`命令，执行更新。
然后执行下面的命令，检查当前的JDK配置。
```bash
$ java -version
```

删除压缩包
```bash
$ rm jdk1.7.0_65.tar.gz
```
>说明：
实际操作过程中，使用的`XShell`连接linux机器。
执行`source /etc/profile`以后，系统的JDK环境并没有改变。
执行source命令以后，关闭当前session，重新打开即可。

---
**将以上操作，运用到集群中所有机器上。**

### 2. Hadoop修改

下载或者重新编译`hadoop-2.5.0.tar.gz`，将包放到`/home`下面并解压
```bash
$ tar zxvf hadoop-2.5.0.tar.gz
```
删除压缩包
```bash
$ rm hadoop-2.5.0.tar.gz
```


**配置Hadoop：**

首先修改`/etc/profile`文件，添加
```bash
#Hadoop Setting
export HADOOP_HOME=/home/hadoop-2.5.0
export PATH=$PATH:$HADOOP_HOME/bin
```

进入Hadoop配置目录
```bash
$ cd /home/hadoop-2.5.0/etc/hadoop
```
修改以下配置文件：

 1. hadoop-env.sh
 2. yarn-env.sh
 3. core-site.xml
 4. hdfs-site.xml
 5. mapred-site.xml 若没有该文件，可以将 mapred-site.xml.template 转换
 6. yarn-site.xml
 7. slaves 文件


编辑 hadoop-env.sh
```bash
# The java implementation to use.
export JAVA_HOME=/usr/local/jdk1.7.0_65
```

编辑 yarn-env.sh
```bash
# some Java parameters
export JAVA_HOME=/usr/local/jdk1.7.0_65
```

编辑 core-site.xml
```xml
<configuration>
  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://master63:9000</value>
  </property>
  <property>
    <name>io.file.buffer.size</name>
    <value>131072</value>
  </property>
  <property>
    <name>hadoop.tmp.dir</name>
    <value>file:/data/tmp</value>
    <description>Abase for other temporary directories.</description>
  </property>
  <property>
    <name>hadoop.proxyuser.hduser.hosts</name>
    <value>*</value>
  </property>
  <property>
    <name>hadoop.proxyuser.hduser.groups</name>
    <value>*</value>
  </property>
  <property>
    <name>io.compression.codecs</name>
    <value>org.apache.hadoop.io.compress.GzipCodec,
	       org.apache.hadoop.io.compress.DefaultCodec,
	       org.apache.hadoop.io.compress.BZip2Codec
    </value>
  </property>
</configuration>
```

编辑 hdfs-site.xml
```xml
<configuration>
  <property>
    <name>dfs.namenode.secondary.http-address</name>
    <value>master63:9001</value>
  </property>
  <property>
    <name>dfs.namenode.name.dir</name>
    <value>file:/data/dfs/name</value>
  </property>
  <property>
    <name>dfs.datanode.data.dir</name>
    <value>file:/data/dfs/data</value>
  </property>
  <property>
    <name>dfs.replication</name>
    <value>3</value>
  </property>
  <property>
    <name>dfs.webhdfs.enabled</name>
    <value>true</value>
  </property>
</configuration>
```

编辑 mapred-site.xml
```xml
<configuration>
  <property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
  </property>
  <property>
    <name>mapreduce.jobhistory.address</name>
    <value>master63:10020</value>
  </property>
  <property>
    <name>mapreduce.jobhistory.webapp.address</name>
    <value>master63:19888</value>
  </property>
</configuration>
```

编辑 yarn-site.xml
```xml
<configuration>

<!-- Site specific YARN configuration properties -->
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property>
  <property>
    <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
    <value>org.apache.hadoop.mapred.ShuffleHandler</value>
  </property>
  <property>
    <name>yarn.resourcemanager.address</name>
    <value>master63:8032</value>
  </property>
  <property>
    <name>yarn.resourcemanager.scheduler.address</name>
    <value>master63:8030</value>
  </property>
  <property>
    <name>yarn.resourcemanager.resource-tracker.address</name>
    <value>master63:8031</value>
  </property>
  <property>
    <name>yarn.resourcemanager.admin.address</name>
    <value>master63:8033</value>
  </property>
  <property>
    <name>yarn.resourcemanager.webapp.address</name>
    <value>master63:8088</value>
  </property>
</configuration>
```
 
编辑 slaves 文件
```bash
slave64
slave66
slave67
```

---
**将以上操作，运用到集群中所有机器上。**

启动hadoop，测试运行情况。

### 3. HBase修改
下载或者重新编译`hbase-0.98.6.1-bin.tar.gz`，将包放到`/home`下面并解压
```bash
$ tar zxvf hbase-0.98.6.1-bin.tar.gz
```
删除压缩包
```bash
$ rm hbase-0.98.6.1-bin.tar.gz
```
**配置HBase：**

首先修改`/etc/profile`文件，添加
```bash
#HBase Setting
HBASE_HOME=/home/hbase-0.98.6.1
export PATH=$PATH:$HBASE_HOME/bin
```
进入HBase配置目录
```bash
$ cd /home/hbase-0.98.6.1/conf
```
修改以下配置文件：

 1. hbase-env.sh
 2. hbase-site.xml
 3. regionservers 文件

修改 `hbase-env.sh`
```bash
# The java implementation to use. Java 1.6 required.
export JAVA_HOME=/usr/local/jdk1.7.0_65
# Tell HBase whether it should manage it's own instance of Zookeeper or not.
export HBASE_MANAGES_ZK=true
```

修改 `hbase-env.xml`
```xml
<configuration>
	<property>
		<name>hbase.tmp.dir</name>
		<value>/data/hbase_tmp</value>
	</property>
	<property>
		<name>hbase.rootdir</name>
		<value>hdfs://master63:9000/hbase</value>
	</property>
	<property>
		<name>hbase.cluster.distributed</name>
		<value>true</value>
	</property>
	<property>
		<name>hbase.zookeeper.quorum</name>
		<value>slave64,slave66,slave67</value>
	</property>
</configuration>
```

修改 `regionservers`
```xml
slave64
slave66
slave67
```
---
**将以上操作，运用到集群中所有机器上。**

测试启动。

#### 问题1：部分机器未启动
只有一个机器起了server。在未启动的机器中，Hbase log记录了错误信息如下：
```bash
2014-12-12 14:46:56,369 FATAL [regionserver60020] regionserver.HRegionServer: Master rejected startup because clock is out of sync
org.apache.hadoop.hbase.ClockOutOfSyncException: org.apache.hadoop.hbase.ClockOutOfSyncException: Server slave67,60020,1418366813077 has been rejected; Reported time is too far out of sync with master.  Time difference of 33233ms > max allowed of 30000ms
```
原因是，两个未启动的机器的时间和master不同步。

>解决方式：
对集群中所有的机器同步时间即可。


### 4. Hive修改
下载或者重新编译`apache-hive-0.13.1-bin.tar.gz`，将包放到`/home`下面并解压
```bash
$ tar zxvf apache-hive-0.13.1-bin.tar.gz
```
删除压缩包
```bash
$ rm apache-hive-0.13.1-bin.tar.gz
```
**配置Hive：**

Hive将元数据存在关系数据库中。这里选用的是`mysql`：

 1. 将`mysql-connector-java-5.1.34-bin.jar`上传到/home/apache-hive-0.13.1-bin/lib
 2. 配置好MySQL数据库，创建数据库`hive63`


接下来，修改`/etc/profile`文件，添加
```bash
#Hive Setting
export HIVE_HOME=/home/apache-hive-0.13.1-bin
export PATH=$HIVE_HOME/bin:$PATH
```
进入Hive配置目录
```bash
$ cd /home/apache-hive-0.13.1-bin/conf
```

修改以下配置文件：

 1. hive-site.xml

编辑 hive-site.xml 文件，内容如下：
具体 IP 地址、用户名、密码等根据实际情况填写。
```xml
<property>
	<name>javax.jdo.option.ConnectionURL</name>
	<value>jdbc:mysql://192.168.129.62/hive63?createDatabaseIfNotExist=true</value>
	<description>JDBC connect string for a JDBC metastore</description>
</property>
<property>
	<name>javax.jdo.option.ConnectionDriverName</name>
	<value>com.mysql.jdbc.Driver</value>
	<description>Driver class name for a JDBC metastore</description>
</property>
<property>
	<name>javax.jdo.option.ConnectionUserName</name>
	<value>test</value>
	<description>username to use against metastore database</description>
</property>
<property>
	<name>javax.jdo.option.ConnectionPassword</name>
	<value>test</value>
	<description>password to use against metastore database</description>
</property>
<property>
	<name>hive.metastore.warehouse.dir</name>
	<!-- base hdfs path -->
	<value>hdfs://master63:9000/hive/warehouse</value>
	<description>location of default database for the warehouse</description>
</property>
<property>
	<name>hive.stats.dbclass</name>
	<value>jdbc:mysql</value>
</property>
<property>
	<name>hive.stats.jdbcdriver</name>
	<value>com.mysql.jdbc.Driver</value>
</property>
<property>
	<name>hive.stats.dbconnectionstring</name>
	<value>jdbc:mysql://192.168.129.62:3306/tempstatsstore63</value>
</property>
<property>
	<name>hive.zookeeper.quorum</name>
	<value>slave64,slave66,slave67</value>
	<description>
		The list of ZooKeeper servers to talk to. 
		This is only needed for read/write locks.
	</description>
</property>
<property>
	<name>hive.zookeeper.client.port</name>
	<value>3888</value>
	<description>
		The port of ZooKeeper servers to talk to. 
		This is only needed for read/write locks.
	</description>
</property>
<property>
	<name>hive.metastore.uris</name>
	<value>thrift://192.168.129.70:9083</value>
	<description>
		Thrift uri for the remote metastore. 
		Used by metastore client to connect to remote metastore.
	</description>
</property>
```
拷贝HBase的jar包到Hive(from lib to lib)
```bash
$ cp hbase-client-0.98.6.1.jar       /home/apache-hive-0.13.1-bin/lib/
$ cp hbase-common-0.98.6.1-tests.jar /home/apache-hive-0.13.1-bin/lib/
$ cp hbase-common-0.98.6.1.jar       /home/apache-hive-0.13.1-bin/lib/
$ cp hbase-protocol-0.98.6.1.jar     /home/apache-hive-0.13.1-bin/lib/
$ cp hbase-server-0.98.6.1.jar       /home/apache-hive-0.13.1-bin/lib/
$ cp htrace-core-2.04.jar            /home/apache-hive-0.13.1-bin/lib/
```

---
**将以上操作，运用到集群中所有机器上。**


在 HDFS 上创建文件夹，并把依赖的 jar 包放到文件夹中
```bash
$ hdfs dfs -mkdir /hive
$ hdfs dfs -mkdir /hive/lib
$ hdfs dfs -put   /home/apache-hive-0.13.1-bin/lib/hbase-common-0.98.6.1.jar     /hive/lib
$ hdfs dfs -put   /home/apache-hive-0.13.1-bin/lib/hive-hbase-handler-0.13.1.jar /hive/lib
$ hdfs dfs -put   /home/apache-hive-0.13.1-bin/lib/zookeeper-3.4.6.jar           /hive/lib
```

>Hive依赖HBase中的zookeeper，
需要先启动Hadoop，再启动HBase，最后启动Hive

hive 启动流程
```bash
$ nohup hive --service metastore > $HIVE_HOME/log/hive_metastore.log &
```
hive 远程服务启动（编程需要）
```bash
$ nohup hive --service hiveserver > $HIVE_HOME/log/hiveserver.log &
```

### 5. Sqoop修改

下载tar包到指定位置，并解压

配置环境变量
编辑`/etc/profile`文件，在结尾加入如下代码
```bash
export SQOOP_HOME=/home/sqoop-1.4.5
export PATH=$PATH:$SQOOP_HOME/bin
```
复制lib包：
包括 sqoop 和 mysql-connector
```bash
$ cp sqoop-1.4.5.jar /home/hadoop-2.5.0/share/hadoop/common
$ cp mysql-connector-java-5.1.34-bin.jar /home/hadoop-2.5.0/share/hadoop/common
```

修改  `sqoop-env.sh`(从 sqoop-env-template.sh 转换)

```bash
#Set path to where bin/hadoop is available
export HADOOP_COMMON_HOME=/home/hadoop-2.5.0 

#Set path to where hadoop-*-core.jar is available
export HADOOP_MAPRED_HOME=/home/hadoop-2.5.0 

#set the path to where bin/hbase is available
export HBASE_HOME=/home/hbase-0.98.6.1

#Set the path to where bin/hive is available
export HIVE_HOME=/home/apache-hive-0.13.1-bin

#Set the path for where zookeper config dir is (使用的是Hbase自带的zookeeper，暂不配置)
#export ZOOCFGDIR=
```



