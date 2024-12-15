#  zookeeper集群部署

### 1、解压并改名

```shell
[root@bigdata1 software]# tar -zxvf apache-zookeeper-3.5.7-bin.tar.gz  -C /opt/module/
[root@bigdata1 module]# mv apache-zookeeper-3.5.7-bin/ zookeeper-3.5.7
```

### 2、配置服务器编号

```shell
[root@bigdata1 zookeeper-3.5.7]# mkdir zkData
[root@bigdata1 zookeeper-3.5.7]# cd zkData
[root@bigdata1 zkData]# vim myid
1
```

### 3、将zk分发到其他两节点

```
将zookeeper-3.5.7分发到其他两个节点。
[root@bigdata2 module]# scp -r /opt/module/zookeeper-3.5.7 bigdata2:/opt/module
[root@bigdata3 module]# scp -r /opt/module/zookeeper-3.5.7 bigdata3:/opt/module

修改其他两台的myid： 路径：cd /opt/module/zookeeper-3.5.7/zkData/myid
[root@bigdata2 zkData]# vim myid
2
[root@bigdata3 zkData]# vim myid
3

```

### 4、配置zoo.cfg文件（/opt/module/zookeeper-3.5.7/conf）

```
#进入目录：
[root@bigdata1 conf]# cd /opt/module/zookeeper-3.5.7/conf
#重命名文件：
[root@bigdata1 conf]# cp zoo_sample.cfg zoo.cfg
#配置zoo.cfg：
[root@bigdata1 conf]# vim zoo.cfg
#修改数据存储路径配置
dataDir=/opt/module/zookeeper-3.5.7/zkData
```

### 5、分发zoo.cfg文件

```bash
[root@bigdata1 conf]# scp zoo.cfg bigdata2:/opt/module/zookeeper-3.5.7/conf
[root@bigdata1 conf]# scp zoo.cfg bigdata3:/opt/module/zookeeper-3.5.7/conf
```

### 6、修改三台机器的zoo.cfg配置文件

**添加配置**

```bash
[root@bigdata1 conf]# vim zoo.cfg

# bigdata1
server.1=0.0.0.0:2888:3888
server.2=bigdata2:2888:3888
server.3=bigdata3:2888:3888
```

```bash
[root@bigdata2 ~]# vim /opt/module/zookeeper-3.5.7/conf/zoo.cfg

# bigdata2
server.1=bigdata1:2888:3888
server.2=0.0.0.0:2888:3888
server.3=bigdata3:2888:3888
```

```bash
[root@bigdata3 ~]# vim /opt/module/zookeeper-3.5.7/conf/zoo.cfg

# bigdata3
server.1=bigdata1:2888:3888
server.2=bigdata2:2888:3888
server.3=0.0.0.0:2888:3888
```

### 6、配置环境变量

配置环境变量：（三台全部配）

```
[root@bigdata1 conf]# vim /etc/profile.d/bigdata.sh
export ZOOKEEPER_HOME=/opt/module/zookeeper-3.5.7
export PATH=$PATH:$ZOOKEEPER_HOME/bin
```

刷新环境变量，（三台都要执行）

```
source /etc/profile
```



7. 启动Zookeeper集群

```shell
（1）分别启动Zookeeper  （三台都要执行）
[root@bigdata1 zookeeper-3.5.7]# zkServer.sh start
[root@bigdata2 zookeeper-3.5.7]# zkServer.sh start
[root@bigdata3 zookeeper-3.5.7]# zkServer.sh start

# 查看状态（三台都要查看）
 zkServer.sh status

```

三台节点中，两个follower。一个leader。



jps：（三台）

```
[root@bigdata1 module]# jps
329 Jps
173 QuorumPeerMain
```

都有QuorumPeerMain





