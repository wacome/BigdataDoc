# Hadoop完全分布式配置安装
### 1 、解压hadoop
```bash
[root@bigdata1 software]# tar -zxvf /opt/module/hadoop-3.1.3.tar.gz -C /opt/module/
```
### 2 、环境变量配置
```bash
[root@bigdata1 module]# vim /etc/profile
```
```bash
#HADOOP_HOME
export HADOOP_HOME=/opt/module/hadoop-3.1.3
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
```

```bash
[root@bigdata1 module]# source /etc/profile
```

### 3 、配置hadoop

#### 3.1为Hadoop提供JAVA解释器路径信息，为Yarn任务、资源管理器提供Java运行环境


```bash
[root@bigdata1 module]# cd /opt/module/hadoop-3.1.3/etc/hadoop/
[root@bigdata1 hadoop]# vim hadoop-env.sh
export JAVA_HOME=/usr/java/jdk1.8.0_221       # 可以在vim命令模式输入/export JAVA_HOME，快速找到要修改的位置，或者在最上面添加
[root@bigdata1 hadoop]# vim yarn-env.sh
export JAVA_HOME=/usr/java/jdk1.8.0_221       # 若jdk路径和这里的不同，需要修改
```

#### 3.2配置HDFS主节点信息、持久化和数据文件的主目录

```bash
[root@bigdata1 hadoop]# vim core-site.xml
```

```xml
<!-- 配置hadoop文件系统-->
<property>
        <name>fs.defaultFS</name>
        <value>hdfs://bigdata1:9000</value>
</property>
<!-- 配置hadoop临时目录-->
<property>
        <name>hadoop.tmp.dir</name>
        <value>/opt/module/hadoop-3.1.3/tmp</value>
</property>
```

#### 3.3配置HDFS默认的数据存放策略

```bash
[root@bigdata1 hadoop]# vim hdfs-site.xml
```

```xml
<!-- 配置文件副本数 -->
<property>
        <name>dfs.replication</name>
        <value>2</value>
</property>
 <!-- 配置SecondaryNameNode服务器的主机ip和端口 |yarn地址-->
<property>
        <name>dfs.namenode.secondary.http-address</name>
        <value>bigdata1:50090</value>
</property>
<!-- 下面为新增要求-->
 <!-- namenode 的 web 端访问端口配置为 15887-->
<property>
        <name>dfs.namenode.http-address</name>
        <value>bigdata1:15887</value>
</property>
```

#### 3.4配置mapreduce任务调度策略

```bash
[root@bigdata1 hadoop]# cp mapred-site.xml.template mapred-site.xml
[root@bigdata1 hadoop]# vim mapred-site.xml
```

```xml
<!-- 配置mapreduce计算框架-->
<property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
</property>
```

#### 3.5配置Yarn资源管理角色的信息

```bash
[root@bigdata1 hadoop]# vim yarn-site.xml
```

```xml
<!-- 指定Reducer获取数据的方式 -->
<property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
</property>
<!-- yarn主节点rm的位置-->
<property>
        <name>yarn.resourcemanager.hostname</name>
        <value>bigdata1</value>
</property>
<!--是否启动一个线程检查每个任务正使用的物理内存量，如果任务超出分配值，则直接将其杀掉，默认 是 true -->
<property>
        <name>yarn.nodemanager.pmem-check-enabled</name>
        <value>false</value>
</property>
<!-- nm虚拟内存检查，默认为true，会导致任务被kill，设置为false 关闭-->
<property>
        <name>yarn.nodemanager.vmem-check-enabled</name>
        <value>false</value>
</property>
```

#### 3.6配置datanode节点信息

```bash
[root@bigdata1 hadoop]# vim workers
```

```
bigdata1
bigdata2
bigdata3
```

#### 3.7配置hadoop使用root启动

```bash
[root@bigdata1 hadoop-3.1.3]# vim start-dfs.sh
```

在文件中添加以下内容：

```
HDFS_DATANODE_USER=root
HDFS_NAMENODE_USER=root
HDFS_SECONDARYNAMENODE_USER=root
```

同理，`stop-dfs.sh`中也需要添加上面的内容，此处不再赘述。

下面，修改`start-yarn.sh`文件

```bash
[root@bigdata1 hadoop]# vim start-yarn.sh
```

```
YARN_RESOURCEMANAGER_USER=root
YARN_NODEMANAGER_USER=root
```

同理，`stop-yarn.sh`中也需要添加上面的内容，此处不再赘述。

#### 3.8分发hadoop及环境变量

```bash
[root@bigdata1 hadoop]# scp /etc/profile bigdata2:/etc/profile
profile												100% 2008   831.6KB/s   00:00			# 此为成功的提示
[root@bigdata1 hadoop]# scp /etc/profile bigdata3:/etc/profile
profile												100% 2008   612.5KB/s   00:00			# 此为成功的提示
Tips:分发环境变量后一定要在分发的两个节点刷新一下 (source /etc/profile)
[root@bigdata1 hadoop]# scp -r /opt/module/hadoop-3.1.3/ bigdata2:/opt/modules
[root@bigdata1 hadoop]# scp -r /opt/module/hadoop-3.1.3/ bigdata3:/opt/modules
```

### 4 、格式化hadoop

```bash
[root@bigdata1 hadoop-3.1.3]# hadoop namenode -format
```

终端输出：

```bash
22/11/28 11:23:12 INFO namenode.FSImage: Allocated new BlockPoolId: BP- 1254736198-192.168.239.157-1669605792805
22/11/28 11:23:12 INFO common.Storage: Storage directory /opt/hadoop- 3.1.3/tmp/dfs/name has been successfully formatted. 											# 出现successfully formatted则证明格式化成功
22/11/28 11:23:12 INFO namenode.FSImageFormatProtobuf: Saving image file
/opt/hadoop-3.1.3/tmp/dfs/name/current/fsimage.ckpt_0000000000000000000 using no
compression
22/11/28 11:23:13 INFO namenode.FSImageFormatProtobuf: Image file /opt/hadoop-
2.7.7/tmp/dfs/name/current/fsimage.ckpt_0000000000000000000 of size 321 bytes
saved in 0 seconds.
22/11/28 11:23:13 INFO namenode.NNStorageRetentionManager: Going to retain 1
images with txid >= 0
22/11/28 11:23:13 INFO util.ExitUtil: Exiting with status 0
22/11/28 11:23:13 INFO namenode.NameNode: SHUTDOWN_MSG:
/************************************************************
SHUTDOWN_MSG: Shutting down NameNode at bigdata1/192.168.239.157
************************************************************/
[root@bigdata1 hadoop-3.1.3]#
```

### 5 、启动hadoop，并查看各节点进程

##### 任务五：启动Hadoop集群，查看bigdata1节点jps进程，将查看结果复制粘贴至对应报告中。

```bash
[root@bigdata1 hadoop-3.1.3]# start-all.sh
```
```bash
[root@bigdata1 hadoop-3.1.3]# jps
10067 NameNode
10340 SecondaryNameNode
10485 ResourceManager
10597 NodeManager
10841 Jps
10189 DataNode
```
```bash
[root@bigdata2 usr]# jps
10099 DataNode
10195 NodeManager
10295 Jps
```
```bash
[root@bigdata3 usr]# jps
10065 Jps
9869 DataNode
9965 NodeManager
```

> [!NOTE]
> [参考官方文档](https://hadoop.apache.org/docs/r2.7.7/hadoop-project-dist/hadoop-common/ClusterSetup.html)