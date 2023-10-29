#  zookeeper集群部署

### 1、解压并改名

```shell
[root@master software]# tar -zxvf apache-zookeeper-3.5.7-bin.tar.gz  -C /opt/modules/
[root@master modules]# mv apache-zookeeper-3.5.7-bin/ zookeeper-3.5.7
```

### 2、配置服务器编号

```shell
[root@master zookeeper-3.5.7]# mkdir zkData
[root@master zookeeper-3.5.7]# cd zkData
[root@master zkData]# vi myid
1
```

### 3、将zk分发到其他两节点

```
将zookeeper-3.5.7分发到其他两个节点。
[root@master modules]# scp -r /opt/modules/zookeeper-3.5.7 slave1:/opt/modules
[root@master modules]# scp -r /opt/modules/zookeeper-3.5.7 slave2:/opt/modules

修改其他两台的myid： 路径：cd /opt/modules/zookeeper-3.5.7/zkData/myid
[root@slave1 zkData]# vi myid
2
[root@slave2 zkData]# vi myid
3

```

### 4、配置zoo.cfg文件（/opt/modules/zookeeper-3.5.7/conf）

```
#进入目录：
[root@master conf]# cd /opt/modules/zookeeper-3.5.7/conf
#重命名文件：
[root@master conf]# cp zoo_sample.cfg zoo.cfg
#配置zoo.cfg：
[root@master conf]# vi zoo.cfg
#修改数据存储路径配置
dataDir=/opt/modules/zookeeper-3.5.7/zkData
#添加配置
server.1=master:2888:3888
server.2=slave1:2888:3888
server.3=slave2:2888:3888
```

6. 向slave1和slave2同步zoo.cfg文件

```shell

[root@master conf]# scp -r /opt/modules/zookeeper-3.5.7/conf/zoo.cfg slave1:/opt/modules/zookeeper-3.5.7/conf
[root@master conf]# scp -r /opt/modules/zookeeper-3.5.7/conf/zoo.cfg slave2:/opt/modules/zookeeper-3.5.7/conf
```



环境变量：（三台全部配）

```
[root@master conf]# vi /etc/profile.d/bigdata_env.sh
export ZOOKEEPER_HOME=/opt/modules/zookeeper-3.5.7
export PATH=$PATH:$ZOOKEEPER_HOME/bin
```

```
source /etc/profile
```



7. 启动Zookeeper集群

```shell
（1）分别启动Zookeeper  （三台都要执行）
[root@master zookeeper-3.5.7]# zkServer.sh start
[root@slave1 zookeeper-3.5.7]# zkServer.sh start
[root@slave2 zookeeper-3.5.7]# zkServer.sh start

# 查看状态（三台都要查看）
 zkServer.sh status

```

三台节点中，两个follower。一个leader。



jps：（三台）

```
[root@slave2 modules]# jps
329 Jps
173 QuorumPeerMain
```

都有QuorumPeerMain





