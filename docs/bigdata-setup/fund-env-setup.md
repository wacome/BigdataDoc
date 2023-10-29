# 基础环境配置

## 1、虚拟机VMware安装

TODO

## 2、Centos操作系统安装

TODO

## 3、配置网络并连接ssh工具

TODO

## 4、基础配置

### 4.1、关闭防火墙（三台都要关闭）

```bash
systemctl stop firewalld #关闭防火墙
systemctl status firewalld #查看防火墙状态
systemctl disable firewalld #永久禁用防火墙
```

### 4.2、hosts配置及分发

任务三:请完成host相关配置，将三个节点分别命名为master、slave1、slave2，并做免密登录，使用 绝对路径从Master节点复制JDK解压后的安装文件到Slave1、Slave2节点，并配置相关环境变量，将全 部复制命令复制并粘贴至对应报告中;

### 4.2.1、修改主机名

```bash
#master
[root@localhost ~]# hostnamectl set-hostname master
[root@localhost ~]# bash
[root@master ~]#
#slave1
[root@localhost ~]# hostnamectl set-hostname slave1
[root@localhost ~]# bash
[root@slave1 ~]#
#slave2
[root@localhost ~]# hostnamectl set-hostname slave2
[root@localhost ~]# bash
[root@slave2 ~]#
```

### 4.2.2、设置hosts

> [!TIP]设置hosts其实是设置主机名与 ip 地址的映射，这里添加的 ip 地址是自己设置的

```bash
#master
[root@master ~]# vi /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.239.157 master
192.168.239.158 slave1
192.168.239.159 slave2
#slave1
[root@slave1 ~]# vi /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.239.157 master
192.168.239.158 slave1
192.168.239.159 slave2
#slave2
[root@slave2 ~]# vi /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.239.157 master
192.168.239.158 slave1
192.168.239.159 slave2
```

### 4.2.3、设置ssh免密连接（三台都要做）

```bash
#master
[root@master ~]# ssh-keygen -t rsa
[root@master ~]# ssh-copy-id master
[root@master ~]# ssh-copy-id slave1
[root@master ~]# ssh-copy-id slave2
#slave1
[root@slave1 ~]# ssh-keygen -t rsa
[root@slave1 ~]# ssh-copy-id master
[root@slave1 ~]# ssh-copy-id slave1
[root@slave1 ~]# ssh-copy-id slave2
#slave2
[root@slave2 ~]# ssh-keygen -t rsa
[root@slave2 ~]# ssh-copy-id master
[root@slave2 ~]# ssh-copy-id slave1
[root@slave2 ~]# ssh-copy-id slave2
```

## 5、JDK安装

### 5.1、解压JDK

任务一:将Master 节点JDK 安装包解压并移动到/usr/java 路径(若路径不存在，则需新建)，将命令复制 并粘贴至对应报告中;

```bash
#上传jdk安装包到/opt/software目录下(若没有，则新建)
[root@master ~]# cd /opt/
[root@master opt]# mkdir software
[root@master ~]# cd /usr/
[root@master usr]# mkdir java				#创建/usr/java路径
#解压tar包到当前目录
[root@modules ~]# tar -zxvf /opt/modules/jdk-8u221-linux-x64.tar.gz -C /usr/java
[root@modules ~]# cd /usr/java
[root@master java]# mv jdk1.8.0_221/ jdk			#重命名配置
```

### 5.2、配置环境变量

> [!TIP]
>
> 这里的配置环境变量主要是将软件的执行路径加入 `PATH`环境变量中， `PATH` 环境变量定义了 shell 在查找命令时应该搜索的目录，这样执行软件的命令可以不用指定完整路径。
>
> **三台机子都要配置**

任务二:1、修改/etc/profile 文件，设置 JDK 环境变量，配置完毕后在Master 节点分别执行“java”和 “javac”命令，将命令行执行结果分别截图并粘贴至对应报告 中;

```bash
[root@master java]# vi /etc/profile
```
```bash
#JAVA_HOME
export JAVA_HOME=/usr/java/jdk
export PATH=$PATH:$JAVA_HOME/bin
```
```bash
[root@master java]# source /etc/profile 		#刷新环境变量
```

> [!TIP]
>
> 修改过/etc/profile 文件后，一定要记得刷新环境变量，三台机子都要刷新

### 5.4、验证安装

```
[root@master java]# javac
```

<img src="../assets/javac.png" alt="javac" style="zoom:50%;" />

```bash
#查看jdk版本
[root@master java]# java -version
java version "1.8.0_221"
Java(TM) SE Runtime Environment (build 1.8.0_221-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.221-b11, mixed mode)
```

### 5.3、分发jdk

```bash
#由于其他两个子节点没有/usr/java路径，需自创
[root@slave1 usr]# mkdir java
[root@slave2 usr]# mkdir java
#分发jdk
[root@master ~]# scp -r /usr/java/jdk1.8.0_221/ slave1:/usr/java/
[root@master ~]# scp -r /usr/java/jdk1.8.0_221/ slave2:/usr/java/
```

### 5.4、从节点验证jdk

```bash
#slave1
[root@slave1 usr]# java -version
java version "1.8.0_221"
Java(TM) SE Runtime Environment (build 1.8.0_221-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.221-b11, mixed mode)
#slave2
[root@slave2 usr]# java -version
java version "1.8.0_221"
Java(TM) SE Runtime Environment (build 1.8.0_221-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.221-b11, mixed mode)
```
