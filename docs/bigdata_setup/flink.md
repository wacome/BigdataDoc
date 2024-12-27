# Flink on yarn安装配置

## 1、解压安装Flink

```bash
[root@master software]# tar -zxvf flink-1.14.0-bin-scala_2.12.tgz -C /opt/module
[root@master module]# mv flink-1.14.0-bin-scala_2.12/ flink
```

## 2、配置环境变量

```bash
[root@master module]# vi /etc/profile.d/bigdata.sh
#FLINK_HOME
export FLINK_HOME=/opt/module/flink
export PATH=$PATH:$FLINK_HOME/bin
export HADOOP_CLASSPATH=`hadoop classpath`
export HADOOP_CONF_DIR=${HADOOP_HOME}/etc/hadoop
[root@master opt]# source /etc/profile
```

## 3、启动Hadoop集群

**Flink on Yarn模式基于Hadoop集群Yarn。**

## 4、Per Job Cluster

开 启 Hadoop 集 群， 在 yarn 上 以 per job 模 式 运 行 $FLINK_HOME/examples/batch/WordCount.jar，将运行结果复制粘贴至粘贴至对应报告中。

> [!NOTE]
>
> Flink的Per Job Cluster模式（单作业集群模式）下，一个Job会对应一个集群，每提交一个作业会根据自身的情况，都会单独向yarn申请资源，直到作 业执行完成，一个作业的失败与否并不会影响下一个作业的正常提交和运行。独享Dispatcher和 ResourceManager，按需接受资源申请；适合规模大长时间运行的作业。 每次提交都会创建一个新的flink集群，任务之间互相独立，互不影响，方便管理。任务执行完成之 后创建的集群也会消失。

### 4.1启动hadoop集群

确认是否启动： jps

如果没启：执行 `start-all.sh`

**该模式不启动yarn-session（不需要预先在 YARN 上启动一个 Flink session），直接执行job**

### 4.2测试

```bash
[root@master module]# yum install -y nc
#使用 netcat 在端口 22222 上创建一个监听（-l 参数代表监听）并保持开启（-k 参数代表保持开启）的 TCP 连接
[root@master module]# nc -lk 22222
#另起一个终端执行一下命令
[root@master ~]# flink run -m yarn-cluster
/opt/module/flink/examples/batch/WordCount.jar --hostname master --port 22222
#执行结果如下
(a,5)
(action,1)
(after,1)
(against,1)
(all,2)
(and,12)
(arms,1)
(arrows,1)
(awry,1)
(ay,1)
(bare,1)
···
```

> [!TIP]
> 
> 若出现以下错误：
> ```
> Exception in thread “Thread-5” java.lang.IllegalStateException: Trying to access closed classloader Please check if you store classloaders directly or indirectly in static fields. If the stacktrace suggests that the leak occurs in a third party library and cannot be fixed immediately, you can disable this check with the configuration ‘classloader.check-leaked-classloader’.
> ```
> 在flink-conf.yaml配置文件中增加如下设置
> ```bash
> [root@master flink]# vim conf/flink-conf.yaml 
> classloader.check-leaked-classloader: false
> ```
>

---

> [!TIP]
> 若之前配置的hadoop为高可用，则可能出现以下错误：
> ```
> Caused by: org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.ipc.StandbyException): Operation category READ is not supported in state standby.
> ```
> 解决方法：
> 将主节点自动故障转移至active状态
> ```bash
> [root@bigdata1 conf]# hdfs haadmin -failover nn2 nn1 
> Failover to NameNode at bigdata1/192.168.45.10:8020 successful
> ```
>