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

## 5、修改java文件

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

注意添加spark3.1,scala-2.12的编译参数，需要在hudi-0.11.0目录下执行以下命令

```bash
[root@bigdata1 hudi-0.11.0]# mvn clean package -DskipTests -Pspark3.1 -Pscala-2.12 -o
```

结果：

```
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  07:00 min
[INFO] Finished at: 2024-12-14T01:06:16+08:00
[INFO] ------------------------------------------------------------------------
```

> [!TIP]
> 注意：编译过程中可能会出现以下错误
> ```
> Caused by: org.apache.maven.project.DependencyResolutionException: Could not resolve dependencies for project org.apache.hudi:hudi-utilities_2.12:jar:0.11.0: The following artifacts could not be resolved: io.confluent:kafka-avro-serializer:jar:5.3.4, io.confluent:common-config:jar:5.3.4, io.confluent:common-utils:jar:5.3.4, io.confluent:kafka-schema-registry-client:jar:5.3.4: Cannot access internal (/opt/software/RepMaven/) in offline mode and the artifact io.confluent:kafka-avro-serializer:jar:5.3.4 has not been downloaded from it before.
> ```
> 解决方法：
> 删除掉/opt/software/RepMaven/目录下的所有依赖里面的`*.lastUpdated`文件和`_remote.repositories`文件
> ```bash
> find /opt/software/RepMaven -name "*.lastUpdated" -type f -delete
> find /opt/software/RepMaven -name "_remote.repositories" -type f -delete
> ```
> 

## 7、集成Spark

```bash
[root@bigdata1 hudi-0.11.0]# cp packaging/hudi-spark-bundle/target/hudi-spark3.1-bundle_2.12-0.11.0.jar /opt/module/spark-yarn/jars/
```

## 8、启动spark-shell

启动之前要确保hadoop和spark已经启动
在启动 spark-shell 时添加 Kryo 序列化器配置，否则运行 hudi 会报错

```bash
[root@bigdata1 hudi-0.11.0]# spark-shell --conf "spark.serializer=org.apache.spark.serializer.KryoSerializer"
```

## 9、测试

输入题目要求代码测试

```scala
import org.apache.hudi.QuickstartUtils._
import scala.collection.JavaConversions._
import org.apache.spark.sql.SaveMode._
import org.apache.hudi.DataSourceReadOptions._
import org.apache.hudi.DataSourceWriteOptions._
import org.apache.hudi.config.HoodieWriteConfig._
import org.apache.hudi.common.model.HoodieRecord

val tableName = "hudi_trips_cow"
val basePath = "file:///tmp/hudi_trips_cow"
val dataGen = new DataGenerator

val inserts = convertToStringList(dataGen.generateInserts(10))
val df = spark.read.json(spark.sparkContext.parallelize(inserts, 2))
df.write.format("hudi").
  options(getQuickstartWriteConfigs).
  option(PRECOMBINE_FIELD_OPT_KEY, "ts").
  option(RECORDKEY_FIELD_OPT_KEY, "uuid").
  option(PARTITIONPATH_FIELD_OPT_KEY, "partitionpath").
  option(org.apache.hudi.config.HoodieWriteConfig.TABLE_NAME, tableName).
  mode(Overwrite).
  save(basePath)

val tripsSnapshotDF = spark.read.format("hudi").load(basePath + "/*/*/*/*")
tripsSnapshotDF.createOrReplaceTempView("hudi_trips_snapshot")
spark.sql("select fare, begin_lon, begin_lat, ts from  hudi_trips_snapshot where fare > 20.0").show()
```

> [!TIP]
> 输入上面代码需要使用spark-shell的多行输入模式，输入`:paste`
> ```bash
> scala> :paste
> ```
> 代码输入完成之后，回车一行，然后使用`Ctrl+D`退出多行输入模式
>

运行结果：

```
+------------------+-------------------+-------------------+-------------+      
|              fare|          begin_lon|          begin_lat|           ts|
+------------------+-------------------+-------------------+-------------+
| 33.92216483948643| 0.9694586417848392| 0.1856488085068272|1733585995710|
| 27.79478688582596| 0.6273212202489661|0.11488393157088261|1733892666661|
| 93.56018115236618|0.14285051259466197|0.21624150367601136|1733860507041|
| 64.27696295884016| 0.4923479652912024| 0.5731835407930634|1733803846264|
| 66.62084366450246|0.03844104444445928| 0.0750588760043035|1733817154618|
|34.158284716382845|0.46157858450465483| 0.4726905879569653|1733891735629|
|  43.4923811219014| 0.8779402295427752| 0.6100070562136587|1733828881499|
| 41.06290929046368| 0.8192868687714224|  0.651058505660742|1733892627514|
+------------------+-------------------+-------------------+-------------+

import org.apache.hudi.QuickstartUtils._
import scala.collection.JavaConversions._
import org.apache.spark.sql.SaveMode._
import org.apache.hudi.DataSourceReadOptions._
import org.apache.hudi.DataSourceWriteOptions._
import org.apache.hudi.config.HoodieWriteConfig._
import org.apache.hudi.common.model.HoodieRecord
tableName: String = hudi_trips_cow
basePath: String = file:///tmp/hudi_trips_cow
dataGen: org.apache.hudi.QuickstartUtils.DataGenerator = org.apache.hudi.QuickstartUtils$DataGenerator@47b541a3
inserts: java.util.List[String] = [{"ts": 1733891735629, "uuid": "1c441f2d-6d0b-42bc-874f-27cf0947541c", "rider": "rider-213", "driver": "driver-213", "begin_lat": 0.4726905879569653, ...
```
