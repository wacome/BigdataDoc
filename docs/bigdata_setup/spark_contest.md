# Spark 组件部署管理
### Spark on yarn模式

> spark客户端连接Yarn，不需要额外构建集群。

#### 1.解压spark

```bash
[root@bigdata1 software]# tar -zxvf spark-3.1.1-bin-hadoop3.2.tgz -C /opt/module/
[root@bigdata1 module]# mv spark-3.1.1-bin-hadoop3.2/ spark

```

#### 2.配置环境变量

```bash
[root@bigdata1 src]# vim /etc/profile.d/bigdata.sh
```

```bash
#SPARK_HOME
export SPARK_HOME=/opt/module/spark
export PATH=$PATH:$SPARK_HOME/bin
```

#### 4.修改spark-env.sh

```bash
[root@bigdata1 conf]# mv spark-env.sh.template spark-env.sh
[root@bigdata1 conf]# vim spark-env.sh
YARN_CONF_DIR=/opt/module/hadoop-3.1.3/etc/hadoop
#注意修改hadoop路径为自己的路径
```

#### 5.修改配置

当机器内存较少时，防止执行过程进行被意外杀死，可以做如下配置：
修改hadoop配置文件/opt/hadoop-3.1.3/etc/hadoop/yarn-site.xml，添加如下内容

```xml
<property>
	<name>yarn.nodemanager.pmem-check-enabled</name>
	<value>false</value>
</property>
<property>
	<name>yarn.nodemanager.vmem-check-enabled</name>
	<value>false</value>
</property
```

#### 6.重启Hadoop

```bash
[root@bigdata1 opt]# stop-all.sh
[root@bigdata1 opt]# start-all.sh
```

#### 7、启动spark集群 （注意执行路径）

```bash
# 注意是具体的sbin路径，而不是直接执行start-all.sh，因为会和hadoop的start-all.sh冲突
[root@bigdata1 spark]# sbin/start-all.sh
[root@bigdata1 spark]# jps
1347 Worker
1273 Master
1402 Jps
```

