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

## 3、修改hbase-site.xml
```bash
[root@bigdata1 module]# cd /opt/module/hbase-2.2.3/
[root@bigdata1 hbase-2.2.3]# vim conf/hbase-site.xml
```

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

## 5、分发hbase目录和环境变量

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

## 6、启动hbase集群

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
> 若启动后，在logs里或者在启动时，出现错误：
> ```
> Caused by: java.lang.NumberFormatException: For input string: "30s"
> ```
> 解决方案：
> 在hdfs-site.xml中修改所有的`30s`为`30`
> ```bash
> [root@bigdata1 ~]# vim /opt/module/hadoop/etc/hadoop/hdfs-site.xml
> ```
> ```xml
> <property>
  <name>dfs.client.datanode-restart.timeout</name>
  <value>30</value>
  </property>
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