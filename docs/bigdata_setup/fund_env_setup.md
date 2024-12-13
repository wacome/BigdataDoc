# 基础环境配置

## 1、虚拟机VMware安装

TODO

## 2、Centos操作系统安装

> [!NOTE]
>
> CentOS是免费的、开源的、可以重新分发的开源操作系统，CentOS（Community Enterprise Operating System，中文意思是社区企业操作系统）是[Linux](https://baike.baidu.com/item/Linux/27050)发行版之一。
>
> CentOS Linux发行版是一个稳定的，可预测的，可管理的和可复现的平台，源于[Red Hat Enterprise Linux](https://baike.baidu.com/item/Red Hat Enterprise Linux/10770503)（RHEL）依照[开放源代码](https://baike.baidu.com/item/开放源代码/114160)规定释出的源码所编译而成。
>
> 自2004年3月以来，CentOS Linux一直是社区驱动的开源项目，旨在与[RHEL](https://baike.baidu.com/item/RHEL/2767838)在功能上兼容。

下载CentOS 7的iso文件：http://mirrors.cqu.edu.cn/CentOS/7.9.2009/isos/x86_64/ ，找到  [CentOS-7-x86_64-DVD-2009.iso](http://mirrors.cqu.edu.cn/CentOS/7.9.2009/isos/x86_64/CentOS-7-x86_64-DVD-2009.iso) 下载。

### 2.1 点击右上角文件，新建虚拟机

<img src="../assets/vm1.png" alt="vm1" style="zoom:67%;" />

### 2.2 选择典型

<img src="../assets/vm2.png" alt="vm2" style="zoom:67%;" />

### 2.3 点击浏览选择iso安装文件的目录

<img src="../assets/vm3.png" alt="vm3" style="zoom:67%;" />

### 2.4 输入信息

<img src="../assets/vm4.png" alt="vm4" style="zoom:67%;" />

**全名是指主机名**

### 2.5 更改安装路径

<img src="../assets/vm6.png" alt="vm6" style="zoom:67%;" />

### 2.6 设置磁盘大小及存储类型

<img src="../assets/vm7.png" alt="vm7" style="zoom:67%;" />

### 2.7 完成

<img src="../assets/vm8.png" alt="vm8" style="zoom:67%;" />

### 2.8 等待安装

<img src="../assets/vm9.png" alt="vm9" style="zoom:67%;" />

## 3、配置网络并连接ssh工具

> [!NOTE]
>
> 配置静态ip，便于连接ssh工具及后续安装操作

### 3.1 打开虚拟网络编辑器

<img src="../assets/ip1.png" alt="ip1" style="zoom:67%;" />

### 3.2 点击更改设置

<img src="../assets/ip2.png" alt="ip2" style="zoom:67%;" />

### 3.3 配置ip及子网掩码

<img src="../assets/ip3.png" alt="ip3" style="zoom: 50%;" />

### 3.4 点击NAT设置

<img src="../assets/ip4.png" alt="ip4" style="zoom:67%;" />

### 3.5 配置网关

> [!TIP]
>
> 一般来说，网关ip的网络部分即图中192.168.157部分与ip地址相同，最后一位配置为1或其他数字，但不能和ip冲突

<img src="../assets/ip5.png" alt="ip5" style="zoom:67%;" />

### 3.6 配置系统网络适配器

打开设置，选择网络，可以看到里面有VMnet8，点击编辑

<img src="../assets/ip6.png" alt="ip6" style="zoom: 50%;" />

### 3.7 配置ipv4地址

<img src="../assets/ip7.png" alt="ip7" style="zoom: 67%;" />

**将ip地址配置为刚才设置的，但是末位不能为0**

<img src="../assets/ip13.png" alt="ip13" style="zoom: 67%;" />

### 3.8 打开终端

进入centos系统桌面，右键桌面，打开terminal

<img src="../assets/ip9.png" alt="ip9" style="zoom: 67%;" />

### 3.9 查看ip

刚打开的terminal比较小，可以按`Ctrl`+`Shift`+`=`键放大

<img src="../assets/ip10.png" alt="ip10" style="zoom: 67%;" />

`ens33`为网卡，ip地址为192.168.157.128

### 3.10 修改网卡配置文件

进入root模式

```bash
[toycon@bogon ~]$ su
```

输入密码（注意密码不显示出来）回车即可

```bash
[root@bogon ~]$ vim /etc/sysconfig/network-scripts/ifcfg-ens33
```

**BOOTPROTO配置为static，ONBOOT设置为yes，添加四行配置信息，ip地址(IPADDR)，网关(GATEWAY)，子网掩码(NETMASK) 这些都要改成自己的，DNS1保持不变**

```
TYPE="Ethernet"
PROXY_METHOD="none"
BROWSER_ONLY="no"
BOOTPROTO="static"	# 改为static
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
IPV6_ADDR_GEN_MODE="stable-privacy"
NAME="ens33"
UUID="73fcb4cf-239e-4263-bb7b-c13d07b8a4a5"
DEVICE="ens33"
ONBOOT="yes"	# 此处设置为yes
# 添加这四行
IPADDR=192.168.157.128
GATEWAY=192.168.157.2
NETMASK=255.255.255.0
DNS1=8.8.8.8
```

### 3.11 重启网络服务

```bash
[root@bogon toycon]# systemctl restart network
```

### 3.12 可以ping一下外网

```bash
[root@bogon toycon]# ping www.baidu.com
```

<img src="../assets/ip14.png" alt="ip14" style="zoom:67%;" />

#### 若ping不通，则需要配置一下DNS

打开虚拟网络编辑器，点击NAT设置

<img src="../assets/ip15.png" alt="ip15" style="zoom:67%;" />

然后点击DNS设置

<img src="../assets/ip16.png" alt="ip16" style="zoom:67%;" />

设置DNS为8.8.8.8，确定即可

<img src="../assets/ip17.png" alt="ip17" style="zoom:67%;" />

**然后设置centos7的DNS**

```bash
[root@localhost toycon]# vim /etc/resolv.conf
```

```
nameserver 8.8.8.8		# 设置为这个即可
```

保存退出，再ping一下外网就能ping通了

## 4、基础配置

### 4.1、关闭防火墙（三台都要关闭）

```bash
systemctl stop firewalld #关闭防火墙
systemctl status firewalld #查看防火墙状态
systemctl disable firewalld #永久禁用防火墙
```

### 4.2、hosts配置及分发

任务三:请完成host相关配置，将三个节点分别命名为bigdata1、bigdata2、bigdata3，并做免密登录，使用 绝对路径从bigdata1节点复制JDK解压后的安装文件到bigdata2、bigdata3节点，并配置相关环境变量，将全 部复制命令复制并粘贴至对应报告中;

### 4.2.1、修改主机名

```bash
#bigdata1
[root@localhost ~]# hostnamectl set-hostname bigdata1
[root@localhost ~]# bash
[root@bigdata1 ~]#
#bigdata2
[root@localhost ~]# hostnamectl set-hostname bigdata2
[root@localhost ~]# bash
[root@bigdata2 ~]#
#bigdata3
[root@localhost ~]# hostnamectl set-hostname bigdata3
[root@localhost ~]# bash
[root@bigdata3 ~]#
```

### 4.2.2、设置hosts

> [!TIP]设置hosts其实是设置主机名与 ip 地址的映射，这里添加的 ip 地址是自己设置的

```bash
#bigdata1
[root@bigdata1 ~]# vim /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.239.157 bigdata1
192.168.239.158 bigdata2
192.168.239.159 bigdata3
#bigdata2
[root@bigdata2 ~]# vim /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.239.157 bigdata1
192.168.239.158 bigdata2
192.168.239.159 bigdata3
#bigdata3
[root@bigdata3 ~]# vi /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.239.157 bigdata1
192.168.239.158 bigdata2
192.168.239.159 bigdata3
```

### 4.2.3、设置ssh免密连接（三台都要做）

```bash
#bigdata1
[root@bigdata1 ~]# ssh-keygen -t rsa
[root@bigdata1 ~]# ssh-copy-id bigdata1
[root@bigdata1 ~]# ssh-copy-id bigdata2
[root@bigdata1 ~]# ssh-copy-id bigdata3
#bigdata2
[root@bigdata2 ~]# ssh-keygen -t rsa
[root@bigdata2 ~]# ssh-copy-id bigdata1
[root@bigdata2 ~]# ssh-copy-id bigdata2
[root@bigdata2 ~]# ssh-copy-id bigdata3
#bigdata3
[root@bigdata3 ~]# ssh-keygen -t rsa
[root@bigdata3 ~]# ssh-copy-id bigdata1
[root@bigdata3 ~]# ssh-copy-id bigdata2
[root@bigdata3 ~]# ssh-copy-id bigdata3
```

## 5、JDK安装

### 5.1、解压JDK

任务一:将bigdata1 节点JDK 安装包解压并移动到/usr/java 路径(若路径不存在，则需新建)，将命令复制 并粘贴至对应报告中;

```bash
#上传jdk安装包到/opt/software目录下(若没有，则新建)
[root@bigdata1 ~]# cd /opt/
[root@bigdata1 opt]# mkdir software
[root@bigdata1 ~]# cd /usr/
[root@bigdata1 usr]# mkdir java				#创建/usr/java路径
#解压tar包到当前目录
[root@bigdata1 ~]# tar -zxvf /opt/software/jdk-8u221-linux-x64.tar.gz -C /usr/java
[root@bigdata1 ~]# cd /usr/java
[root@bigdata1 java]# mv jdk1.8.0_221/ jdk			#重命名配置
```

### 5.2、配置环境变量

> [!TIP]
>
> 这里的配置环境变量主要是将软件的执行路径加入 `PATH`环境变量中， `PATH` 环境变量定义了 shell 在查找命令时应该搜索的目录，这样执行软件的命令可以不用指定完整路径。
>
> **三台机子都要配置**

任务二:1、修改/etc/profile 文件，设置 JDK 环境变量，配置完毕后在bigdata1 节点分别执行“java”和 “javac”命令，将命令行执行结果分别截图并粘贴至对应报告 中;

```bash
[root@bigdata1 java]# vim /etc/profile.d/bigdata.sh
```
```bash
#JAVA_HOME
export JAVA_HOME=/usr/java/jdk
export PATH=$PATH:$JAVA_HOME/bin
```
```bash
[root@bigdata1 java]# source /etc/profile 		#刷新环境变量
```

> [!TIP]
>
> 修改过/etc/profile 文件后，一定要记得刷新环境变量

### 5.4、验证安装

```
[root@bigdata1 java]# javac
```

<img src="../assets/javac.png" alt="javac" style="zoom:50%;" />

```bash
#查看jdk版本
[root@bigdata1 java]# java -version
java version "1.8.0_221"
Java(TM) SE Runtime Environment (build 1.8.0_221-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.221-b11, mixed mode)
```

### 5.3、分发jdk

```bash
#由于其他两个子节点没有/usr/java路径，需自创
[root@bigdata2 usr]# mkdir java
[root@bigdata3 usr]# mkdir java
#分发jdk
[root@bigdata1 ~]# scp -r /usr/java/jdk1.8.0_221/ bigdata2:/usr/java/
[root@bigdata1 ~]# scp -r /usr/java/jdk1.8.0_221/ bigdata3:/usr/java/
```

### 5.4、分发环境变量
```bash
[root@bigdata1 ~]# scp /etc/profile bigdata2:/etc/
[root@bigdata1 ~]# scp /etc/profile bigdata3:/etc/

#刷新环境变量
[root@bigdata2 ~]# source /etc/profile
[root@bigdata3 ~]# source /etc/profile
```

### 5.5、从节点验证jdk

```bash
#bigdata2
[root@bigdata2 usr]# java -version
java version "1.8.0_221"
Java(TM) SE Runtime Environment (build 1.8.0_221-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.221-b11, mixed mode)

#bigdata3
[root@bigdata3 usr]# java -version
java version "1.8.0_221"
Java(TM) SE Runtime Environment (build 1.8.0_221-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.221-b11, mixed mode)
```
