# hudi安装配置

## 1、解压maven和hudi

```bash
[root@bigdata1 software]# tar -zxvf apache-maven-3.6.3-bin.tar.gz -C /opt/module/
[root@bigdata1 software]# tar -zxvf hudi-0.11.0.src.tgz -C /opt/module/

[root@bigdata1 software]# cd /opt/module/
[root@bigdata1 module]# mv apache-maven-3.6.3/ maven-3.6.3
```

## 2、修改maven设置文件

配置maven本地库为/opt/software/RepMaven/
远程仓库使用阿里云镜像

```bash
[root@bigdata1 maven-3.6.3]# vim conf/settings.xml
```
查找到<mirrors>标签，直接添加以下内容（添加到<mirrors>标签里面，注意避开注释符号<!-- -->）

```xml
   <mirror>
     <id>nexus-aliyun</id>
     <mirrorOf>central</mirrorOf>
     <name>Nexus aliyun</name>
     <url>http://maven.aliyun.com/nexus/content/groups/public</url>
   </mirror>
```

在settings.xml文件中添加本地仓库路径，直接添加到<settings>标签里面

```xml
   <localRepository>/opt/software/RepMaven/</localRepository>
```

## 3、配置maven的环境变量

```bash
[root@bigdata1 maven-3.6.3]# vim /etc/profile.d/bigdata.sh

#MAVEN_HOME
export MAVEN_HOME=/opt/module/maven-3.6.3
export PATH=$PATH:$MAVEN_HOME/bin

[root@bigdata1 maven-3.6.3]# source /etc/profile
```

## 4、查看maven版本

```bash
[root@bigdata1 maven-3.6.3]# cd /opt/
[root@bigdata1 opt]# mvn -v
```

## 5、编译hudi源码

### 5.1 替换父模块pom.xml文件

```bash
[root@bigdata1 hudi-0.11.0]# cd /opt/module/hudi-0.11.0
[root@bigdata1 hudi-0.11.0]# vim pom.xml
```

将以下内容替换到pom.xml文件中
```xml

```

### 5.2 修改java文件

```bash
[root@bigdata1 hudi-0.11.0]# vim /opt/module/hudi-0.11.0/hudi-common/src/main/java/org/apache/hudi/common/table/log/block/HoodieParquetDataBlock.java
```
在HoodieParquetDataBlock.java 中修改以下内容
找到`try (FSDataOutputStream outputStream = new FSDataOutputStream(baos)) {`
在`baos`后面添加`null`即可

```java
try (FSDataOutputStream outputStream = new FSDataOutputStream(baos, null)) {
```

## 6、使用maven对Hudi进行构建

注意添加spark3.1,scala-2.12的编译参数

```bash
[root@bigdata1 hudi-0.11.0]# mvn clean package -Pspark3.1,scala-2.12
```

