# Flink on yarn安装配置

## 1、解压安装Flink

任务一：将Flink 包解压到路径/opt/modules 目录下，将完整命令复制粘贴至粘贴至对应报告中;

```bash
[root@master software]# tar -zxvf apache-flink-1.10.2.tar.gz -C /opt/modules
```

## 2、配置环境变量

任务二：修改/etc/profile 文件，设置Flink 环境变量，并使环境变量生效将环境变量配置内容复制粘贴 至粘贴至对应报告中;

```bash
[root@master modules]# vi /etc/profile
#FLINK_HOME
export FLINK_HOME=/opt/modules/flink-yarn
export PATH=$PATH:$FLINK_HOME/bin
export HADOOP_CLASSPATH=`hadoop classpath`
export HADOOP_CONF_DIR=${HADOOP_HOME}/etc/hadoop
[root@master opt]# source /etc/profile
```

## 3、启动Hadoop集群

**Flink on Yarn模式基于Hadoop集群Yarn。**

## 4、Per Job Cluster

任务三：开 启 Hadoop 集 群， 在 yarn 上 以 per job 模 式 运 行 $FLINK_HOME/examples/batch/WordCount.jar，将运行结果复制粘贴至粘贴至对应报告中。

> [!NOTE]
>
> Flink的Per Job Cluster模式（单作业集群模式）下，一个Job会对应一个集群，每提交一个作业会根据自身的情况，都会单独向yarn申请资源，直到作 业执行完成，一个作业的失败与否并不会影响下一个作业的正常提交和运行。独享Dispatcher和 ResourceManager，按需接受资源申请；适合规模大长时间运行的作业。 每次提交都会创建一个新的flink集群，任务之间互相独立，互不影响，方便管理。任务执行完成之 后创建的集群也会消失。

### 4.1启动hadoop集群

确认是否启动： jps

如果没启：执行 start-all.sh

**该模式不启动yarn-session（不需要预先在 YARN 上启动一个 Flink session），直接执行job**

### 4.2测试

```bash
[root@master modules]# yum install -y nc
#使用 netcat 在端口 22222 上创建一个监听（-l 参数代表监听）并保持开启（-k 参数代表保持开启）的 TCP 连接
[root@master modules]# nc -lk 22222
#另起一个终端执行一下命令
[root@master ~]# flink run -m yarn-cluster
/opt/modules/flink/examples/batch/WordCount.jar --hostname master --port 22222
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

