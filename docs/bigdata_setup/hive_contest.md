# Hive安装配置

## 一、配置MySQL（训练服务器上可不用配置）

> [!NOTE]
>
> MySQL比赛环境已经安装好，比赛时无需安装，但是要授予权限，可能操作的命令如下：

### 5、开启mysql服务

```bash
[root@bigdata1 yum.repos.d]# systemctl start mysqld
#开机自启动
[root@bigdata1 yum.repos.d]# systemctl enable mysqld
```

### 6、获取自动生成的随机密码

```bash
[root@bigdata1 yum.repos.d]# grep 'temporary password' /var/log/mysqld.log
2022-11-27T11:02:13.862460Z 1 [Note] A temporary password is generated for
root@localhost: lgu52Ggl8h*#
```
```bash
[root@bigdata1 yum.repos.d]# mysql -uroot -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.7.40
Copyright (c) 2000, 2022, Oracle and/or its affiliates.
Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql>
```

### 7、设置mysql的密码安全策略（若设置强密码，则无需配置）

```mysql
#设置密码强度为低级
mysql> set global validate_password_policy=LOW;
Query OK, 0 rows affected (0.00 sec)

#设置密码长度
mysql> set global validate_password_length=5;
Query OK, 0 rows affected (0.00 sec)

#修改root密码
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';
Query OK, 0 rows affected (0.00 sec)
```

### 8、设置远程登录

```mysql
#以新密码登录mysql
[root@bigdata1 yum.repos.d]# mysql -uroot -p123456
#创建用户
mysql> create user 'root'@'%' identified by '123456';
Query OK, 0 rows affected (0.00 sec)
#允许远程连接(授予权限，很重要!)
mysql> grant all privimleges on *.* to 'root'@'%' with grant option;
Query OK, 0 rows affected (0.00 sec)
#刷新权限
mysql> flush privimleges;
Query OK, 0 rows affected (0.00 sec)
```

## 二、安装hive

### 1、解压hive


```bash
[root@bigdata1 software]# tar -zxvf apache-hive-3.1.2-bin.tar.gz -C /opt/module/
```

**改名**

```bash
[root@bigdata1 module]# mv apache-hive-3.1.2-bin hive-3.1.2
```

### 2、配置环境变量

```bash
[root@bigdata1 module]# vim /etc/profile.d/bigdata.sh
```

```bash
#HIVE_HOME
export HIVE_HOME=/opt/module/hive-3.1.2
export PATH=$PATH:$HIVE_HOME/bin
```

```bash
[root@bigdata1 module]# source /etc/profile
```

添加所需依赖包，将 MySQL 数据库作为 Hive 元数据库。初始化 Hive 元数据，并通过 schematool 相关命令执行初始化

### 3、配置hive

将mysql-connector-java-5.1.48-bin.jar文件上传到hive下的lib目录下

```bash
[root@bigdata1 software]# mv mysql-connector-java-5.1.48-bin.jar /opt/module/hive-3.1.2/lib/
```

修改hive-site.xml文件(没有该文件名，将hive-default.xml.template复制并改名为hive-site.xml)

```bash
[root@bigdata1 module]# cd /opt/module/hive-3.1.2/conf
[root@bigdata1 conf]# cp hive-default.xml.template hive-site.xml
```

```bash
[root@bigdata1 conf]# vim hive-site.xml
```

```xml
<configuration>
<!--需要登录MySQL数据库，创建一个 hive 数据库备用-->
	<property>
        <name>javax.jdo.option.ConnectionURL</name>
        <value>jdbc:mysql://bigdata1:3306/hive?
createDatabaseIfNotExist=true&amp;useSSL=false</value>
	</property>
<!--安装MySQL数据库的驱动类-->
	<property>
        <name>javax.jdo.option.ConnectionDriverName</name>
        <value>com.mysql.jdbc.Driver</value>
	</property>
<!--安装MySQL数据库的名称-->
	<property>
        <name>javax.jdo.option.ConnectionUserName</name>
        <value>root</value>
	</property>
<!--安装MySQL数据库的密码-->
	<property>
        <name>javax.jdo.option.ConnectionPassword</name>
        <value>123456</value>
	</property>

<!--cli 显示表头和列名-->
<!--若无要求，可以不用配置-->
	<property>
        <name>hive.cli.print.header</name>
        <value>true</value>
	</property>
	<property>
        <name>hive.cli.print.current.db</name>
        <value>true</value>
	</property>
</configuration>
```

### 4、初始化mysql元数据库

```bash
[root@bigdata1 conf]# cd ..
[root@bigdata1 hive-3.1.2]# bin/schematool -dbType mysql -initSchema
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/opt/hive/lib/log4j-slf4j-impl-
2.6.2.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/opt/hadoop/share/hadoop/common/lib/slf4j-
log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.apache.logging.slf4j.Log4jLoggerFactory]
Metastore connection URL:    jdbc:mysql://bigdata1:3306/hive?
createDatabaseIfNotExist=true&useSSL=false
Metastore Connection Driver :    com.mysql.jdbc.Driver
Metastore connection User:   root
Starting metastore schema initialization to 2.3.0
Initialization script hive-schema-2.3.0.mysql.sql
Initialization script completed
schemaTool completed
```

**以下问题，若初始化元数据库未发生，需注意之后启动hive是否有对应的错误**

> [!TIP]
>
> 若出现以下错误，则需要解决guava包的冲突问题
> ```
> java.lang.NoSuchMethodError: com.google.common.base.Preconditions.checkArgument
> ```
> 解决方法：
> 删除hive中低版本的guava-19.0.jar包，将hadoop中的guava-27.0-jre.jar复制到hive的lib目录下即可
> ```bash
> [root@bigdata1 hive-3.1.2]# rm -f lib/guava*
> ```
> ```bash
> [root@bigdata1 lib]# cp /opt/module/hadoop-3.1.3/share/hadoop/common/lib/guava-27.0-jre.jar /opt/module/hive-3.1.2/lib/
> ```
>

---

> [!TIP]
> 若出现以下错误，需要进conf/hive-site.xml文件中删除几个字符
> 
> ```
> Exception in thread "main" java.lang.RuntimeException: com.ctc.wstx.exc.WstxParsingException: Illegal character entity: expansion character (code 0x8
 at [row,col,system-id]: [3215,96,"file:/opt/module/hive/conf/hive-site.xml"]
> ```
> 打开hive-site.xml文件，删除第3215行的`&#8;;`这几个字符：
> 
> 可以直接在vim中使用`:3215`跳转到3215行
> 
> ```xml
>  <property>
    <name>hive.txn.xlock.iow</name>
    <value>true</value>
    <description>
      Ensures commands with OVERWRITE (such as INSERT OVERWRITE) acquire Exclusive locks for&#8;;（前面这里删除）transactional tables.  This ensures that inserts (w/o overwrite) running concurrently
      are not hidden by the INSERT OVERWRITE.
    </description>
  </property>
> ```
> 

---

> [!TIP]
>
> 若出现以下错误，则需要解决hive-site.xml中变量未定义的问题
> 
> ```
> Caused by: java.net.URISyntaxException: Relative path in absolute URI: ${system:java.io.tmpdir%7D/$%7Bsystem:user.name%7D
> ```
> 解决方法：
> 将以下内容添加到hive-site.xml中
>
> ```xml
>   <property>
>    <name>system:java.io.tmpdir</name>
>    <value>/tmp/hive/java</value>
>  </property>
>  <property>
>    <name>system:user.name</name>
>    <value>${user.name}</value>
>  </property>
> ```
>

---

> [!TIP]
>
> 若出现以下错误，则需要取mysql中新建hive数据库
>
> ```
> org.apache.hadoop.hive.metastore.HiveMetaException: Failed to get schema version.
> Underlying cause: com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException : Unknown database 'hive'
> SQL Error code: 1049
> Use --verbose for detailed stacktrace.
> *** schemaTool failed ***
> ```
>
> 创建数据库：
>
> ```bash
> [root@bigdata1 hive-3.1.2]# mysql -uroot -p123456
> ```
>
> ```mysql
> mysql> create database hive;
> ```
>

### 5、启动hive

```bash
[root@bigdata1 hive]# hive 					#注意:启动hive之前一定要启动hadoop
hive>
#查看数据库，测试启动是否成功
hive (default)> show databases;
OK
default
Time taken: 6.924 seconds, Fetched: 1 row(s)
hive (default)>
```

若查看数据库未成功，比如下方错误：

org.apache.hadoop.hive.ql.metadata.HiveException: java.lang.RuntimeException: Unable to instantiate org.apache.hadoop.hive.ql.metadata.SessionHiveMetaStoreClient

则可能是hive的metastore服务未开启

**开启metastore服务：**

```bash
nohup hive --service metastore > /dev/null 2>&1 &
```

- `nohup` 是一个 Linux 命令，它用于运行一个命令，同时忽略所有挂断（HUP）信号。这意味着即使你关闭了终端，命令也会继续运行。
- `>` 是一个重定向操作符，它将命令的输出重定向到一个文件。在这个例子中，我们将输出重定向到 `/dev/null`，这是一个特殊的文件，所有写入它的数据都会被丢弃。
- `2>&1` 是一个重定向操作符，它将标准错误（file descriptor 2）重定向到标准输出（file descriptor 1）。在这个例子中，这意味着所有的错误信息也会被写入 `/dev/null`。
- `&` 是一个 shell 操作符，它将命令放到后台运行。这意味着你可以在命令运行的同时，执行其他的命令。