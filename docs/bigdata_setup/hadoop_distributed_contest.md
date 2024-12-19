# Hadoop完全分布式配置安装
### 1 、解压hadoop
```bash
[root@bigdata1 module]# tar -zxvf /opt/software/hadoop-3.1.3.tar.gz -C /opt/module/
```
### 2 、环境变量配置
```bash
[root@bigdata1 module]# vim /etc/profile.d/bigdata.sh
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

#### 3.1提取hadoop默认配置文件
```bash
# 提取 core-default.xml,mapred-default.xml,yarn-default.xml
[root@bigdata1 hadoop-3.1.3]# cd share/hadoop/client
[root@bigdata1 client]# jar xf hadoop-client-api-3.1.3.jar core-default.xml
[root@bigdata1 client]# jar xf hadoop-client-api-3.1.3.jar mapred-default.xml
[root@bigdata1 client]# jar xf hadoop-client-api-3.1.3.jar yarn-default.xml

# 提取 hdfs-default.xml
[root@bigdata1 hadoop-3.1.3]# cd share/hadoop/hdfs
[root@bigdata1 hdfs]# jar xf hadoop-hdfs-3.1.3.jar hdfs-default.xml

# 重命名文件
[root@bigdata1 hdfs]# mv hdfs-default.xml hdfs-site.xml
[root@bigdata1 client]# mv core-default.xml core-site.xml
[root@bigdata1 client]# mv mapred-default.xml mapred-site.xml
[root@bigdata1 client]# mv yarn-default.xml yarn-site.xml

# 复制并替换原有配置文件
[root@bigdata1 hdfs]# cp -f hdfs-site.xml /opt/module/hadoop-3.1.3/etc/hadoop/
[root@bigdata1 client]# cp -f mapred-site.xml /opt/module/hadoop-3.1.3/etc/hadoop/
[root@bigdata1 client]# cp -f yarn-site.xml /opt/module/hadoop-3.1.3/etc/hadoop/
[root@bigdata1 client]# cp -f core-site.xml /opt/module/hadoop-3.1.3/etc/hadoop/

```

#### 3.2配置HDFS数据文件的主目录

```bash
[root@bigdata1 hadoop]# vim core-site.xml
```
**使用vim的查找命令`/`查找对应修改内容**

```xml
<!-- 配置hadoop文件系统，只需修改value标签包裹的值-->
<property>
        <name>fs.defaultFS</name>
        <value>hdfs://bigdata1:8020</value>
</property>

<property>
        <name>fs.default.name</name>
        <value>hdfs://bigdata1:8020</value>
</property>
```

#### 3.3配置HDFS默认的数据存放策略（若题目未要求，无需配置）

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

#### 3.4配置mapreduce任务调度策略（若题目未要求，无需配置）

```bash
[root@bigdata1 hadoop]# vim mapred-site.xml
```

```xml
<!-- 配置mapreduce计算框架-->
<property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
</property>
```

#### 3.5配置Yarn资源管理角色的信息（若题目未要求，无需配置）

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

若题目要求配置三个datanode节点，则写入bigdata1、bigdata2、bigdata3节点。
若只配置两个datanode节点，则写入bigdata2、bigdata3节点。

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
[root@bigdata1 hadoop-3.1.3]# vim sbin/start-dfs.sh
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
[root@bigdata1 hadoop-3.1.3]# vim sbin/start-yarn.sh
```

```
YARN_RESOURCEMANAGER_USER=root
YARN_NODEMANAGER_USER=root
```

同理，`stop-yarn.sh`中也需要添加上面的内容，此处不再赘述。

#### 3.8分发hadoop及环境变量

```bash
[root@bigdata1 hadoop]# scp /etc/profile.d/bigdata.sh bigdata2:/etc/profile.d/
profile												100% 2008   831.6KB/s   00:00			# 此为成功的提示
[root@bigdata1 hadoop]# scp /etc/profile.d/bigdata.sh bigdata3:/etc/profile.d/
profile												100% 2008   612.5KB/s   00:00			# 此为成功的提示
Tips:分发环境变量后一定要在分发的两个节点刷新一下 (source /etc/profile)
[root@bigdata1 hadoop]# scp -r /opt/module/hadoop-3.1.3/ bigdata2:/opt/module/
[root@bigdata1 hadoop]# scp -r /opt/module/hadoop-3.1.3/ bigdata3:/opt/module/
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
3.1.3/tmp/dfs/name/current/fsimage.ckpt_0000000000000000000 of size 321 bytes
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
[root@bigdata1 usr]# jps
10099 DataNode
10195 NodeManager
10295 Jps
```
```bash
[root@bigdata2 usr]# jps
10065 Jps
9869 DataNode
9965 NodeManager
```