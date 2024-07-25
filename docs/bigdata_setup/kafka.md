# Kafka安装配置

任务二：完善其他配置并分发kafka文件到slave1,slave2中，并在每个节点启动Kafka，将Master节点的 Kafka启动命令复制粘贴至对应报告中。

## 解压kafka

```bash
[root@master software]# tar -zxvf kafka_2.11-2.0.0.gz -C /opt/modules/
[root@master modules]# mv kafka_2.11-2.0.0/ kafka
```

## 配置环境变量

```bash
[root@master modules]# vi /etc/profile
#KAFKA_HOME
export KAFKA_HOME=/opt/modules/kafka
export PATH=$PATH:$KAFKA_HOME/bin
[root@master modules]# source /etc/profile
#分发环境变量，注意在各个节点刷新环境变量
[root@master ~]# scp /etc/profile slave1:/etc/profile
[root@master ~]# scp /etc/profile slave1:/etc/profile
```

## 修改server.properties文件

任务一：修改Kafka的server.properties文件，并将修改的内容复制粘贴至对应报告中；

```
[root@master opt]# cd /opt/modules/kafka/config/
[root@master config]# vi server.properties
zookeeper.connect=master:2181,slave1:2181,slave2:2181
broker.id=0 		#（slave1上写1，slave2上写2）注意，每个节点不能写相同的数字，否则会报错
```

任务二：完善其他配置并分发kafka文件到slave1,slave2中，并在每个节点启动Kafka，将Master节点的 Kafka启动命令复制粘贴至对应报告中。

## 分发kafka

```bash
[root@master ~]# scp -r /opt/modules/kafka/ slave1:/opt/modules/
[root@master ~]# scp -r /opt/modules/kafka/ slave2:/opt/modules/
```

## 启动kafka

**1.启动kafka之前一定要先启动 zookeeper**

```bash
cd /opt/modules/zookeeper/bin		#到目录下

zkServer.sh start（三台都要启动）

[root@master bin]# zkServer.sh start
JMX enabled by default
Using config: /opt/modules/zookeeper/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
```

**2.启动 kafka**

```bash
cd /opt/modules/kafka/bin
#每个节点都要启动

[root@master bin]# kafka-server-start.sh /opt/modules/kafka/config/server.properties
```

master启动成功结果如下图：

<img src="../assets/success1.png" alt="success1" style="zoom: 50%;" />

slave1启动成功结果如下图：

<img src="../assets/success2.png" alt="success2" style="zoom: 50%;" />

slave2启动成功结果如下图：

<img src="../assets/success3.png" alt="success3" style="zoom: 50%;" />