#  hbase集群部署

## 1、解压hbase

```bash
[root@bigdata1 module]# tar -zxvf hbase-2.2.3-bin.tar.gz -C /opt/module/
```

## 2、配置环境变量

```bash
[root@bigdata1 module]# vim /etc/profile.d/bigdata.sh
```

```bash
#HBASE_HOME
export HBASE_HOME=/opt/module/hbase-2.2.3
export PATH=$PATH:$HBASE_HOME/bin
```

## 3、提取hbase-default.xml

```bash
[root@bigdata1 hbase-2.2.3]# cd lib
[root@bigdata1 lib]# jar xf hbase-common-2.2.3.jar hbase-default.xml

# 改名为hbase-site.xml
[root@bigdata1 lib]# mv hbase-default.xml hbase-site.xml

# 复制到hbase目录下
[root@bigdata1 lib]# cp -f hbase-site.xml /opt/module/hbase-2.2.3/conf/
```

## 3、修改hbase-site.xml
```bash
[root@bigdata1 module]# cd /opt/module/hbase-2.2.3/
[root@bigdata1 hbase-2.2.3]# vim conf/hbase-site.xml
```

**使用vim的查找命令`/`查找对应修改内容**

```xml
<configuration>
    <property>
        <name>hbase.rootdir</name>
        <value>hdfs://bigdata1:8020/hbase</value>
    </property>
    <property>
        <name>hbase.cluster.distributed</name>
        <value>true</value>
    </property>
    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>bigdata1,bigdata2,bigdata3</value>
    </property>
</configuration>
```

## 4、修改regionservers文件

```bash
[root@bigdata1 hbase-2.2.3]# vim conf/regionservers
```

```bash
bigdata1
bigdata2
bigdata3
```

## 5、修改hbase-env.sh

```bash
[root@bigdata1 hbase-2.2.3]# vim conf/hbase-env.sh
```

将下面`HBASE_MANAGES_ZK`的配置内容注释取消掉，并配置为`false`

```bash
# Tell HBase whether it should manage it's own instance of ZooKeeper or not.                
export HBASE_MANAGES_ZK=false
```

## 6、分发hbase目录和环境变量

```bash
[root@bigdata1 hbase-2.2.3]# scp -r /opt/module/hbase-2.2.3 bigdata2:/opt/module/
[root@bigdata1 hbase-2.2.3]# scp -r /opt/module/hbase-2.2.3 bigdata3:/opt/module/
```

```bash
[root@bigdata1 hbase-2.2.3]# scp /etc/profile.d/bigdata.sh bigdata2:/etc/profile.d/
[root@bigdata1 hbase-2.2.3]# scp /etc/profile.d/bigdata.sh bigdata3:/etc/profile.d/

# 刷新环境变量
source /etc/profile
```

## 7、启动hbase集群

**启动hbase之前一定要先启动 hadoop 和 zookeeper**

```bash
[root@bigdata1 hbase-2.2.3]# start-hbase.sh
```

启动成功结果：

```bash
[root@bigdata1 hbase-2.2.3]# jps
20228 Jps
20231 HMaster
20232 HRegionServer
```

> [!TIP]
> 若出现以下错误，则需要解决hadoop中的时间单位的配置项有问题
> ERROR [main] regionserver.HRegionServer: Failed construction RegionServer
java.lang.NumberFormatException: For input string: "30s"
> 解决方案：
> ```bash
> [root@bigdata1 hadoop]# vim hdfs-site.xml
> #查找所有的30s，将其改为30，去掉`s`，应该有两处
> 改完之后，分发更新其他机器的hdfs-site.xml文件，重启hadoop集群
> ```
> 

> [!TIP]
> 若启动后，HMaster只存活了一会，之后就挂了，查看日志，发现错误为：The procedure WAL relies on the ability to hsync for proper operation during component failures, but the underlying filesystem does not support doing so. Please check the config value of 'hbase.procedure.store.wal.use.hsync' to set the desired level of robustness and ensure the config value of 'hbase.wal.dir' points to a FileSystem mount that can provide it.
> 解决方案：
> 在hbase-site.xml中添加如下配置
> ```xml
> <property>
>  <name>hbase.unsafe.stream.capability.enforce</name>
>  <value>false</value>
> </property>
> ```
> **修改后将该文件同步到其他节点**
>

## 8、查看命名空间

正确启动后，输入以下命令进入`hbase shell`，查看是否有默认的命名空间

```bash
[root@bigdata1 hbase-2.2.3]# hbase shell

hbase(main):001:0> list_namespace                                                           
NAMESPACE                                                                                   
default                                                                                     
hbase                                                                                       
2 row(s)                                                                                    
Took 0.3310 seconds 
```