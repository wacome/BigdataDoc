# flume安装配置

## 1. 解压

```bash
[root@bigdata1 software]# tar -zxvf flume-1.9.0-bin.tar.gz -C /opt/module/

[root@bigdata1 software]# cd /opt/module/
[root@bigdata1 module]# mv apache-flume-1.9.0-bin/ flume
```

## 2. 配置环境变量
```bash
[root@bigdata1 module]# vim /etc/profile.d/bigdata.sh

#FLUME_HOME
export FLUME_HOME=/opt/module/flume
export PATH=$PATH:$FLUME_HOME/bin

[root@bigdata1 module]# source /etc/profile
```

## 3. 查看版本

```bash
[root@bigdata1 module]# flume-ng version
```

## 4、使用flume传输Hadoop日志

在flume的conf目录下创建flume-hdfs.conf文件

```bash
[root@bigdata1 module]# cd flume/conf
[root@bigdata1 conf]# vim flume-hdfs.conf
```

```conf
# agent上的组件名称
a1.sources = r1
a1.sinks = k1
a1.channels = c1
# 配置source
a1.sources.r1.type = exec
a1.sources.r1.command = tail -F /opt/module/hadoop-3.1.3/logs/hadoop-root-namenode-bigdata1.log
# 配置sink
a1.sinks.k1.type = hdfs
a1.sinks.k1.hdfs.path = hdfs://bigdata1:8020/tmp/flume/
a1.sinks.k1.hdfs.fileType = DataStream
# 使用内存作为通道
a1.channels.c1.type = memory
a1.channels.c1.capacity = 2000
a1.channels.c1.transactionCapacity = 200
# 绑定source和sink到channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```

启动Flume传输Hadoop日志（namenode或datanode日志），查看HDFS中/tmp/flume目录下生成的内容

```bash
[root@bigdata1 flume]# flume-ng agent -n a1 -c conf -f conf/flume-hdfs.conf -Dflume.root.logger=INFO,console
```

查看HDFS中/tmp/flume目录下生成的内容，另开一个bigdata1终端执行以下命令

```bash
[root@bigdata1 flume]# hdfs dfs -ls /tmp/flume
```

> [!TIP]
> 
> 若出现以下错误：
> ```
> java.lang.NoSuchMethodError: com.google.common.base.Preconditions.checkArgument(ZLjava/lang/String;Ljava/lang/Object;)V
> ```
> 解决方法：
> 删除flume中低版本的guava-11.0.2.jar包，将hadoop中的guava-27.0-jre.jar复制到hive的lib目录下即可
> ```bash
> [root@bigdata1 flume]# rm -f lib/guava*
> ```
> ```bash
> [root@bigdata1 lib]# cp /opt/module/hadoop-3.1.3/share/hadoop/common/lib/guava-27.0-jre.jar /opt/module/flume/lib/
> ```
>