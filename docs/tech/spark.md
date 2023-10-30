# 1.Spark

## 1.1 什么是RDD

**RDD 是 Spark 提供的最重要的抽象概念，它是一种有容错机制的特殊数据集合，可以分布在集群的结点上，以函数式操作集合的方式进行各种并行操作。
RDD（Resilient Distributed Dataset）叫做弹性分布式数据集，是Spark中最基本的数据抽象。**
**代码中是一个抽象类，它代表一个弹性的、不可变、可分区、里面的元素可并行计算的集合。**

(1) ==弹性==
存储的弹性：内存和磁盘的自动切换；
容错的弹性：数据丢失可以自动恢复；
计算的弹性：计算出错重试机制；
分片的弹性：可根据需要重新分片。

(2) ==分布式==
数据存储在大数据集群的不同节点上

(3) ==数据集,不存储数据==
RDD封装了计算逻辑，并不保存数据

(4) ==数据抽象==
RDD是一个抽象类。需要子类具体实现

(5) ==不可变==
RDD封装了计算逻辑，是不可以改变的，想要改变，只能产生新的RDD，在新的RDD里面封装计算逻辑

(6) 可分区,==并行计算==

## 1.2 RDD特性
> **A list of partitions**
多个分区（一组分区），分区可以看成是数据集的基本组成单位
对于 RDD 来说， 每个分区都会被一个计算任务处理， 并决定了并行计算的粒度。
用户可以在创建 RDD 时指定 RDD 的分区数， 如果没有指定， 那么就会采用默认值。 默认值就是程序所分配到的 CPU Core 的数目.
每个分配的存储是由BlockManager 实现的， 每个分区都会被逻辑映射成 BlockManager 的一个 Block,，而这个 Block 会被一个 Task 负责计算

> **A function for computing each split**
计算每个分区(切片)的函数.
 Spark 中 RDD 的计算是以分片为单位的，每个 RDD 都会实现compute函数以达到这个目的

 > **A list of dependencies on other RDDs**
  RDD之间的依赖关系
 RDD 的每次转换都会生成一个新的 RDD， 所以 RDD 之间会形成类似于流水线一样的前后依赖关系。 在部分分区数据丢失时，Spark 可以通过这个依赖关系重新计算丢失的分区数据， 而不是对 RDD 的所有分区进行重新计算

> **Optionally, a Partitioner for key-value RDDs (e.g. to say that the RDD is hash-partitioned)**
 对存储键值对的 RDD，还有一个可选的分区器
 只有对于 key-value的 RDD，才会有 Partitioner, 非key-value的 RDD 的 Partitioner 的值是 None;Partitioner 不但决定了 RDD 的本区数量, 也决定了 parent RDD Shuffle 输出时的分区数量


# 2. RDD编程
## 2.1 编程模型
在Spark中，RDD被表示为对象，通过对象上的方法调用来对RDD进行转换。RDD经过一系列的transformations转换定义之后，就可以调用actions触发RDD的计算，action可以是向应用程序返回结果，或者是向存储系统保存数据。在Spark中只有遇到action，才会执行RDD的计算(即延迟计算)。

## 2.2 RDD的创建
在Spark中创建RDD的创建方式可以分为三种：从集合中创建RDD、从外部存储创建RDD、从其他RDD创建。

### 2.2.1 IDEA环境准备
1) 创建一个maven工程，工程名称叫SparkCoreTest
2) 在pom文件中添加

```xml
<dependencies>
    <dependency>
        <groupId>org.apache.spark</groupId>
        <artifactId>spark-core_2.12</artifactId>
        <version>3.0.0</version>
    </dependency>
</dependencies>
<build>
    <finalName>SparkCoreTest</finalName>
    <plugins>
        <plugin>
            <groupId>net.alchim31.maven</groupId>
            <artifactId>scala-maven-plugin</artifactId>
            <version>3.4.6</version>
            <executions>
                <execution>
                    <goals>
                        <goal>compile</goal>
                        <goal>testCompile</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```
3) 添加scala框架支持
4) 创建一个Scala文件夹,并把它修改为Source Root
5) 创建包名:com.user1.createrdd

### 2.2.2 从集合中创建
1) 从集合中创建RDD,Spark主要提供了两种函数：parallelize和makeRDD

```java
import org.apache.spark.rdd.RDD
import org.apache.spark.{SparkConf, SparkContext}
object createrdd01_array {
    def main(args: Array[String]): Unit = {
        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")
        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)
        //3.使用parallelize()创建rdd
        val rdd: RDD[Int] = sc.parallelize(Array(1, 2, 3, 4, 5, 6, 7, 8))
        rdd.foreach(println)
        //4.使用makeRDD()创建rdd
        val rdd1: RDD[Int] = sc.makeRDD(Array(1, 2, 3, 4, 5, 6, 7, 8))
        rdd1.foreach(println)
        sc.stop()
    }
}
```

### 2.2.3 从外部存储系统的数据集创建
由外部存储系统的数据集创建RDD包括：本地的文件系统，还有所有Hadoop支持的数据集，比如HDFS、HBase等
1) 数据准备
在新建的SparkCoreTest1项目名称上右键->新建input文件夹->在input文件夹上右键->分别新建1.txt和2.txt。每个文件里面准备一些word单词。
2) 创建RDD

```
import org.apache.spark.rdd.RDD
import org.apache.spark.{SparkConf, SparkContext}
object createrdd02_file {
    def main(args: Array[String]): Unit = {
        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")
        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)
        //3.读取文件。如果是集群路径：hdfs://hadoop102:9000/input
        val lineWordRdd: RDD[String] = sc.textFile("input")
        //4.打印
        lineWordRdd.foreach(println)
        //5.关闭
        sc.stop()
    }
}
```

### 2.2.4 从其他RDD创建
主要是通过一个RDD运算完后，再产生新的RDD。<font color=red>详见2.4节</font>

### 2.2.5 创建IDEA快捷键
1)点击File->Settings…->Editor->Live Templates->output->Live Template
2)点击左下角的Define->选择Scala
3)在Abbreviation中输入快捷键名称scc，在Template text中填写，输入快捷键后生成的内容。
```
//1.创建SparkConf并设置App名称
val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")
//2.创建SparkContext，该对象是提交Spark App的入口
val sc: SparkContext = new SparkContext(conf)
//4.关闭连接
sc.stop()
```

## 2.3 分区规则

### 2.3.1 默认分区源码(RDD数据从集合中创建)
一.默认分区数源码解读
```
val rdd2:RDD[Int] = sc.makeRDD(Array(1,2,3,4,5,6))
```
1)查看makeRDD源码:
2)查看默认分区defaultParallelism源码:
3)defaultParallelism是一个抽象方法，找它的实现类:
4)Ctrl+h查找defaultParallelism的实现类：
5)defaultParallelism是一个抽象方法，找它的实现类：
6)Ctrl+h查找defaultParallelism的实现类：
<font color=red>默认分区数=总的cpu核数（totalCores）</font>
二.代码验证
```
object partition01_default {
    def main(args: Array[String]): Unit = {
        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")
        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)
        val rdd: RDD[Int] = sc.makeRDD(Array(1,2,3,4))
        //3. 输出数据，产生了20个分区
        rdd.saveAsTextFile("output")
println("分区数：" + rdd.partitions.size)
        //4.关闭连接
        sc.stop()
    }
}
产生了8个分区
```
三.思考：数据就4个，分区却产生了8个，严重浪费资源，怎么办？
### 2.3.2 分区源码(RDD数据从集合中创建)
1) 分区测试(RDD数据从集合中创建)

```
object partition02_Array {
    def main(args: Array[String]): Unit = {
        val conf: SparkConf = new SparkConf().setMaster("local[*]").setAppName("SparkCoreTest1")
        val sc: SparkContext = new SparkContext(conf)
        //1）4个数据，设置4个分区，输出：0分区->1，1分区->2，2分区->3，3分区->4
        //val rdd: RDD[Int] = sc.makeRDD(Array(1, 2, 3, 4), 4)
        //2）4个数据，设置3个分区，输出：0分区->1，1分区->2，2分区->3,4
        //val rdd: RDD[Int] = sc.makeRDD(Array(1, 2, 3, 4), 3)
        //3）5个数据，设置3个分区，输出：0分区->1，1分区->2、3，2分区->4、5
        val rdd: RDD[Int] = sc.makeRDD(Array(1, 2, 3, 4, 5), 3)
        rdd.saveAsTextFile("output")
        sc.stop()
    }
}
```
<font color=red>分区的开始位置 = 分区号 * 数据总长度/分区总数</font>
<font color=red>分区的结束位置 =（分区号 + 1）* 数据总长度/分区总数</font>

### 2.3.3 默认分区源码(RDD数据从文件中读取后创建)
1) 分区测试

```
object partition03_file_default {
    def main(args: Array[String]): Unit = {
        val conf: SparkConf = new SparkConf().setMaster("local[*]").setAppName("SparkCoreTest")
        val sc: SparkContext = new SparkContext(conf)
        //1）默认分区的数量：默认取值为当前核数和2的最小值
        //val rdd: RDD[String] = sc.textFile("input")
        rdd.saveAsTextFile("output")
        sc.stop()
    }
}
```
2) 分区源码

### 2.3.4 分区源码(RDD数据从文件中读取后创建)
1) 分区测试

```
object partition04_file {
    def main(args: Array[String]): Unit = {
        val conf: SparkConf = new SparkConf().setMaster("local[*]").setAppName("SparkCoreTest")
        val sc: SparkContext = new SparkContext(conf)
        //1）输入数据1-4，每行一个数字；输出：0=>{1、2} 1=>{3} 2=>{4} 3=>{空}
        //val rdd: RDD[String] = sc.textFile("input/3.txt", 3)
        rdd.saveAsTextFile("output")
        sc.stop()
    }
}
```
<font color=red>注意：getSplits文件返回的是切片规划，真正读取是在compute方法中创建LineRecordReader读取的，有两个关键变量： start = split.getStart()	   end = start + split.getLength</font>

## 2.4 Transformation转换算子(面试开发重点)
RDD整体上分为Value类型、双Value类型和Key-Value类型
### 2.4.1 Value类型
#### 2.4.1.1 map()映射
具体实现
```
object value01_map {
    def main(args: Array[String]): Unit = {
        //1.创建SparkConf并设置App名称
        val conf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")
        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc = new SparkContext(conf)
        //3具体业务逻辑
        // 3.1 创建一个RDD
        val rdd: RDD[Int] = sc.makeRDD(1 to 4,2)
        // 3.2 调用map方法，每个元素乘以2
        val mapRdd: RDD[Int] = rdd.map(_ * 2)
        // 3.3 打印修改后的RDD中数据
        mapRdd.collect().foreach(println)
        //4.关闭连接
        sc.stop()
    }
}
```
#### 2.4.1.2 mapPartitions()以分区为单位执行Map
具体实现
```
object value02_mapPartitions {
    def main(args: Array[String]): Unit = {
        //1.创建SparkConf并设置App名称
        val conf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")
        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc = new SparkContext(conf)
        //3具体业务逻辑
        // 3.1 创建一个RDD
        val rdd: RDD[Int] = sc.makeRDD(1 to 4, 2)
        // 3.2 调用mapPartitions方法，每个元素乘以2
        val rdd1 = rdd.mapPartitions(x=>x.map(_*2))
        // 3.3 打印修改后的RDD中数据
        rdd1.collect().foreach(println)
        //4.关闭连接
        sc.stop()
    }
}
```
#### 2.4.1.3 map()和mapPartitions()区别
>map()：每次处理一条数据
mapPartition()：每次处理一个分区的数据，这个分区的数据处理完后，原RDD中分区的数据才能释放，可能导致OOM
当内存空间较大的时候建议使用mapPartition()，以提高处理效率

#### 2.4.1.4	mapPartitionsWithIndex()带分区号
具体实现
```
object value03_mapPartitionsWithIndex {
    def main(args: Array[String]): Unit = {
        //1.创建SparkConf并设置App名称
        val conf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")
        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc = new SparkContext(conf)
        //3具体业务逻辑
        // 3.1 创建一个RDD
        val rdd: RDD[Int] = sc.makeRDD(1 to 4, 2)
        // 3.2 创建一个RDD，使每个元素跟所在分区号形成一个元组，组成一个新的RDD
        val indexRdd = rdd.mapPartitionsWithIndex( (index,items)=>{items.map( (index,_) )} )
// 3.3 打印修改后的RDD中数据
        indexRdd.collect().foreach(println)
        //4.关闭连接
        sc.stop()
    }
}
```

#### 2.4.1.5 flatMap()压平
具体实现:
```
object value04_flatMap {
    def main(args: Array[String]): Unit = {
        //1.创建SparkConf并设置App名称
        val conf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")
        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc = new SparkContext(conf)
        //3具体业务逻辑
        // 3.1 创建一个RDD
        val listRDD=sc.makeRDD(List(List(1,2),List(3,4),List(5,6),List(7)), 2)
        // 3.2 把所有子集合中数据取出放入到一个大的集合中
        listRDD.flatMap(list=>list).collect.foreach(println)
        //4.关闭连接
        sc.stop()
    }
}
```

#### 2.4.1.6 glom()分区转换数组
具体实现
```
object value05_glom {
def main(args: Array[String]): Unit = {
        //1.创建SparkConf并设置App名称
        val conf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")
        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc = new SparkContext(conf)
        //3具体业务逻辑
        // 3.1 创建一个RDD
        val rdd = sc.makeRDD(1 to 4, 2)
        // 3.2 求出每个分区的最大值  0->1,2   1->3,4
        val maxRdd: RDD[Int] = rdd.glom().map(_.max)
        // 3.3 求出所有分区的最大值的和 2 + 4
        println(maxRdd.collect().sum)
        //4.关闭连接
        sc.stop()
    }
}
```

#### 2.4.1.7 groupBy分组
具体实现
```
object value06_groupby {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc = new SparkContext(conf)

        //3具体业务逻辑
        // 3.1 创建一个RDD
        val rdd = sc.makeRDD(1 to 4, 2)

        // 3.2 将每个分区的数据放到一个数组并收集到Driver端打印
        rdd.groupBy(_ % 2).collect().foreach(println)

        // 3.3 创建一个RDD
        val rdd1: RDD[String] = sc.makeRDD(List("hello","hive","hadoop","spark","scala"))

        // 3.4 按照首字母第一个单词相同分组
        rdd1.groupBy(str=>str.substring(0,1)).collect().foreach(println)

        sc.stop()
    }
}
```
><font color=red>groupBy会存在shuffle过程</font>
<font color=red>shuffle：将不同的分区数据进行打乱重组的过程</font>
<font color=red>shuffle一定会落盘。可以在local模式下执行程序，通过4040看效果。</font>

#### 2.4.1.8 GroupBy 之 WordCount
```
object value06_groupby_wordcount {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc = new SparkContext(conf)

        //3具体业务逻辑
        // 3.1 创建一个RDD
        val strList: List[String] = List("Hello Scala", "Hello Spark", "Hello World")
        val rdd = sc.makeRDD(strList)

        // 3.2 将字符串拆分成一个一个的单词
        val wordRdd: RDD[String] = rdd.flatMap(str=>str.split(" "))

        // 3.3 将单词结果进行转换：word=>(word,1)
        val wordToOneRdd: RDD[(String, Int)] = wordRdd.map(word=>(word, 1))

        // 3.4 将转换结构后的数据分组
        val groupRdd: RDD[(String, Iterable[(String, Int)])] = wordToOneRdd.groupBy(t=>t._1)

        // 3.5 将分组后的数据进行结构的转换
        val wordToSum: RDD[(String, Int)] = groupRdd.map {
            case (word, list) => {
                (word, list.size)
            }
        }

        wordToSum.collect().foreach(println)

        sc.stop()
    }
}
```

#### 2.4.1.9 filter()过滤
代码实现
```
object value07_filter {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3.创建一个RDD
        val rdd: RDD[Int] = sc.makeRDD(Array(1, 2, 3, 4),2)

        //3.1 过滤出符合条件的数据
        val filterRdd: RDD[Int] = rdd.filter(_ % 2 == 0)

        //3.2 收集并打印数据
        filterRdd.collect().foreach(println)

        //4 关闭连接
        sc.stop()
    }
}
```

#### 2.4.1.10 sample()采样
代码实现
```
object value08_sample {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3具体业务逻辑
        // 3.1 创建一个RDD
        val rdd: RDD[Int] = sc.makeRDD(1 to 10)

        // 3.2 打印放回抽样结果
        rdd.sample(true, 0.4, 2).collect().foreach(println)

        // 3.3 打印不放回抽样结果
        rdd.sample(false, 0.2, 3).collect().foreach(println)

        //4.关闭连接
        sc.stop()
    }
}
```

#### 2.4.1.11 distinct()去重
代码实现
```
object value09_distinct {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3具体业务逻辑
        // 3.1 创建一个RDD
        val distinctRdd: RDD[Int] = sc.makeRDD(List(1,2,1,5,2,9,6,1))

        // 3.2 打印去重后生成的新RDD
        distinctRdd.distinct().collect().foreach(println)

        // 3.3 对RDD采用多个Task去重，提高并发度
        distinctRdd.distinct(2).collect().foreach(println)

        //4.关闭连接
        sc.stop()
    }
}
```

#### 2.4.1.12 coalesce()重新分区
Coalesce算子包括：配置执行Shuffle和配置不执行Shuffle两种方式。
1) 不执行Shuffle方式
代码实现

```
object value10_coalesce {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3.创建一个RDD
        //val rdd: RDD[Int] = sc.makeRDD(Array(1, 2, 3, 4), 4)

        //3.1 缩减分区
        //val coalesceRdd: RDD[Int] = rdd.coalesce(2)

        //4. 创建一个RDD
        val rdd: RDD[Int] = sc.makeRDD(Array(1, 2, 3, 4, 5, 6), 3)
        //4.1 缩减分区
        val coalesceRdd: RDD[Int] = rdd.coalesce(2)

        //5 打印查看对应分区数据
        val indexRdd: RDD[Int] = coalesceRdd.mapPartitionsWithIndex(
            (index, datas) => {
                // 打印每个分区数据，并带分区号
                datas.foreach(data => {
                    println(index + "=>" + data)
                })
                // 返回分区的数据
                datas
            }
        )
        indexRdd.collect()

        //6. 关闭连接
        sc.stop()
    }
}
```
2) 执行Shuffle方式

```
//3. 创建一个RDD
val rdd: RDD[Int] = sc.makeRDD(Array(1, 2, 3, 4, 5, 6), 3)
//3.1 执行shuffle
val coalesceRdd: RDD[Int] = rdd.coalesce(2, true)
```
3) Shuffle原理

#### 2.4.1.13 repartition()重新分区(执行Shuffle)
代码实现
```
package com.atguigu.createrdd

import org.apache.spark.rdd.RDD
import org.apache.spark.{SparkConf, SparkContext}

object value11_repartition {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3. 创建一个RDD
        val rdd: RDD[Int] = sc.makeRDD(Array(1, 2, 3, 4, 5, 6), 3)
        //3.1 缩减分区
        //val coalesceRdd: RDD[Int] = rdd.coalesce(2,true)

        //3.2 重新分区
        val repartitionRdd: RDD[Int] = rdd.repartition(2)

        //4 打印查看对应分区数据
        val indexRdd: RDD[Int] = repartitionRdd.mapPartitionsWithIndex(
            (index, datas) => {
                // 打印每个分区数据，并带分区号
                datas.foreach(data => {
                    println(index + "=>" + data)
                })
                // 返回分区的数据
                datas
            }
        )
        indexRdd.collect()

        //6. 关闭连接
        sc.stop()
    }
}
```

#### 2.4.1.14 	coalesce和repartition区别
1) coalesce重新分区，可以选择是否进行shuffle过程。由参数shuffle: Boolean = false/true决定。
2) repartition实际上是调用的coalesce，进行shuffle。源码如下：

```
def repartition(numPartitions: Int)(implicit ord: Ordering[T] = null): RDD[T] = withScope {
    coalesce(numPartitions, shuffle = true)
}
```
3) coalesce一般为缩减分区，如果扩大分区，不使用shuffle是没有意义的，repartition扩大分区执行shuffle。

```
package com.atguigu.createrdd

import org.apache.spark.rdd.RDD
import org.apache.spark.{SparkConf, SparkContext}

object value11_repartition {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3. 创建一个RDD
        val rdd: RDD[Int] = sc.makeRDD(Array(1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11), 3)
        //3.1 合并分区（没有shuffle）
        // coalesce一般为缩减分区，如果扩大分区，不使用shuffle是没有意义的
        //val pRdd: RDD[Int] = rdd.coalesce(4)

        //3.2 重新分区（有shuffle）
        val pRdd: RDD[Int] = rdd.repartition(4)

        //4 打印查看对应分区数据
        val indexRdd: RDD[Int] = pRdd.mapPartitionsWithIndex(
            (index, datas) => {
                // 打印每个分区数据，并带分区号
                datas.foreach(data => {
                    println(index + "=>" + data)
                })
                // 返回分区的数据
                datas
            }
        )
        indexRdd.collect()

        //6. 关闭连接
        sc.stop()
    }
}
```

#### 2.4.1.15 sortBy()排序
代码实现
```
object value12_sortBy {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3具体业务逻辑
        // 3.1 创建一个RDD
        val rdd: RDD[Int] = sc.makeRDD(List(2, 1, 3, 4, 6, 5))
        // 3.2 默认是升序排
        val sortRdd: RDD[Int] = rdd.sortBy(num => num)
        sortRdd.collect().foreach(println)

        // 3.3 配置为倒序排
        val sortRdd2: RDD[Int] = rdd.sortBy(num => num, false)
        sortRdd2.collect().foreach(println)

        // 3.4 创建一个RDD
        val strRdd: RDD[String] = sc.makeRDD(List("1", "22", "12", "2", "3"))

        // 3.5 按照字符的int值排序
        strRdd.sortBy(num => num.toInt).collect().foreach(println)

        // 3.5 创建一个RDD
        val rdd3: RDD[(Int, Int)] = sc.makeRDD(List((2, 1), (1, 2), (1, 1), (2, 2)))

        // 3.6 先按照tuple的第一个值排序，相等再按照第2个值排
        rdd3.sortBy(t=>t).collect().foreach(println)

        //4.关闭连接
        sc.stop()
    }
}
```
### 2.4.2 双Value类型交互

#### 2.4.2.1union()并集
代码实现
```
object DoubleValue01_union {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3具体业务逻辑
        //3.1 创建第一个RDD
        val rdd1: RDD[Int] = sc.makeRDD(1 to 4)

        //3.2 创建第二个RDD
        val rdd2: RDD[Int] = sc.makeRDD(4 to 8)

        //3.3 计算两个RDD的并集
        rdd1.union(rdd2).collect().foreach(println)

        //4.关闭连接
        sc.stop()
    }
}
```

#### 2.4.2.2 subtract ()差集
代码实现
```
object DoubleValue02_subtract {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3具体业务逻辑
        //3.1 创建第一个RDD
        val rdd: RDD[Int] = sc.makeRDD(1 to 4)

        //3.2 创建第二个RDD
        val rdd1: RDD[Int] = sc.makeRDD(4 to 8)

        //3.3 计算第一个RDD与第二个RDD的差集并打印
        rdd.subtract(rdd1).collect().foreach(println)

        //4.关闭连接
        sc.stop()
    }
}
```

#### 2.4.2.3 intersection()交集
代码实现
```
object DoubleValue03_intersection {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3具体业务逻辑
        //3.1 创建第一个RDD
        val rdd1: RDD[Int] = sc.makeRDD(1 to 4)

        //3.2 创建第二个RDD
        val rdd2: RDD[Int] = sc.makeRDD(4 to 8)

        //3.3 计算第一个RDD与第二个RDD的差集并打印
        rdd1.intersection(rdd2).collect().foreach(println)

        //4.关闭连接
        sc.stop()
    }
}
```

#### 2.4.2.4 zip()拉链
需求说明:创建两个RDD,并将两个RDD组合到一起形成一个(k,v)RDD
代码实现
```
object DoubleValue04_zip {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3具体业务逻辑
        //3.1 创建第一个RDD
        val rdd1: RDD[Int] = sc.makeRDD(Array(1,2,3),3)

        //3.2 创建第二个RDD
        val rdd2: RDD[String] = sc.makeRDD(Array("a","b","c"),3)

        //3.3 第一个RDD组合第二个RDD并打印
        rdd1.zip(rdd2).collect().foreach(println)

        //3.4 第二个RDD组合第一个RDD并打印
        rdd2.zip(rdd1).collect().foreach(println)

        //3.5 创建第三个RDD（与1，2分区数不同）
        val rdd3: RDD[String] = sc.makeRDD(Array("a","b"),3)

        //3.6 元素个数不同，不能拉链
        // Can only zip RDDs with same number of elements in each partition
        rdd1.zip(rdd3).collect().foreach(println)

        //3.7 创建第四个RDD（与1，2分区数不同）
        val rdd4: RDD[String] = sc.makeRDD(Array("a","b","c"),2)

        //3.8 分区数不同，不能拉链
        // Can't zip RDDs with unequal numbers of partitions: List(3, 2)
        rdd1.zip(rdd4).collect().foreach(println)

        //4.关闭连接
        sc.stop()
    }
}
```

### 2.4.3 Key-Value类型

#### 2.4.3.1 artitionBy()按照K重新分区
代码实现
```
object KeyValue01_partitionBy {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3具体业务逻辑
        //3.1 创建第一个RDD
        val rdd: RDD[(Int, String)] = sc.makeRDD(Array((1,"aaa"),(2,"bbb"),(3,"ccc")),3)

        //3.2 对RDD重新分区
        val rdd2: RDD[(Int, String)] = rdd.partitionBy(new org.apache.spark.HashPartitioner(2))

        //3.3 查看新RDD的分区数
        println(rdd2.partitions.size)

        //4.关闭连接
        sc.stop()
    }
}
```

#### 2.4.3.2 reduceByKey()按照K聚合V
代码实现
```
object KeyValue02_reduceByKey {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3具体业务逻辑
        //3.1 创建第一个RDD
        val rdd = sc.makeRDD(List(("a",1),("b",5),("a",5),("b",2)))

        //3.2 计算相同key对应值的相加结果
        val reduce: RDD[(String, Int)] = rdd.reduceByKey((x,y) => x+y)

        //3.3 打印结果
        reduce.collect().foreach(println)

        //4.关闭连接
        sc.stop()
    }
}
```

#### 2.4.3.3 groupByKey()按照K重新分组
需求说明：创建一个pairRDD，将相同key对应值聚合到一个seq中，并计算相同key对应值的相加结果。
代码实现
```
object KeyValue03_groupByKey {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3具体业务逻辑
        //3.1 创建第一个RDD
        val rdd = sc.makeRDD(List(("a",1),("b",5),("a",5),("b",2)))

        //3.2 将相同key对应值聚合到一个Seq中
        val group: RDD[(String, Iterable[Int])] = rdd.groupByKey()

        //3.3 打印结果
        group.collect().foreach(println)

        //3.4 计算相同key对应值的相加结果
        group.map(t=>(t._1,t._2.sum)).collect().foreach(println)

        //4.关闭连接
        sc.stop()
    }
}
```

#### 2.4.3.4 reduceByKey和groupByKey区别
1) reduceByKey：按照key进行聚合，在shuffle之前有combine（预聚合）操作，返回结果是RDD[k,v]。
2) groupByKey：按照key进行分组，直接进行shuffle。
3) 开发指导：在不影响业务逻辑的前提下，优先选用reduceByKey。求和操作不影响业务逻辑，求平均值影响业务逻辑。

#### 2.4.3.5 aggregateByKey()按照K处理分区内和分区间逻辑
代码实现
```
object KeyValue04_aggregateByKey {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3具体业务逻辑
        //3.1 创建第一个RDD
        val rdd: RDD[(String, Int)] = sc.makeRDD(List(("a", 3), ("a", 2), ("c", 4), ("b", 3), ("c", 6), ("c", 8)), 2)

        //3.2 取出每个分区相同key对应值的最大值，然后相加
        rdd.aggregateByKey(0)(math.max(_, _), _ + _).collect().foreach(println)

        //4.关闭连接
        sc.stop()
    }
}
```

#### 2.4.3.6 foldByKey()分区内和分区间相同的aggregateByKey()
代码实现
```
object KeyValue05_foldByKey {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3具体业务逻辑
        //3.1 创建第一个RDD
        val list: List[(String, Int)] = List(("a",1),("a",1),("a",1),("b",1),("b",1),("b",1),("b",1),("a",1))
        val rdd = sc.makeRDD(list,2)

        //3.2 求wordcount
        //rdd.aggregateByKey(0)(_+_,_+_).collect().foreach(println)

        rdd.foldByKey(0)(_+_).collect().foreach(println)

        //4.关闭连接
        sc.stop()
    }
}
```

#### 2.4.3.7 combineByKey()转换结构后分区内和分区间操作
1) 需求说明：创建一个pairRDD，根据key计算每种key的均值。（先计算每个key对应值的总和以及key出现的次数，再相除得到结果）
2) 代码实现

```
object KeyValue06_combineByKey {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3.1 创建第一个RDD
        val list: List[(String, Int)] = List(("a", 88), ("b", 95), ("a", 91), ("b", 93), ("a", 95), ("b", 98))
        val input: RDD[(String, Int)] = sc.makeRDD(list, 2)

        //3.2 将相同key对应的值相加，同时记录该key出现的次数，放入一个二元组
        val combineRdd: RDD[(String, (Int, Int))] = input.combineByKey(
            (_, 1),
            (acc: (Int, Int), v) => (acc._1 + v, acc._2 + 1),
            (acc1: (Int, Int), acc2: (Int, Int)) => (acc1._1 + acc2._1, acc1._2 + acc2._2)
        )

        //3.3 打印合并后的结果
        combineRdd.collect().foreach(println)

        //3.4 计算平均值
        combineRdd.map {
            case (key, value) => {
                (key, value._1 / value._2.toDouble)
            }
        }.collect().foreach(println)

        //4.关闭连接
        sc.stop()
    }
}
```

#### 2.4.3.8 reduceByKey、aggregateByKey、foldByKey、combineByKey
>让相同的key进行分区内聚合，让相同key分区间聚合，这里涉及到了分区内预聚合，所以与groupByKey区别在于，groupByKey中shuffle过程数据量不会操作，shuffle落盘文件，相同操作reduceByKey的性能要优于groupByKey
aggregateByKey：
每个分区内key的第一个value与初始值ZoreVaue进行分区内计算
分区内进行计算
分区间进行计算
分区内计算规则与分区间计算不同
foldBykey:
与aggregateByKey类似，只不过foldBykey分区内和分区间的计算规则是一样的
combineByKey:
combineByKey共有三个参数：
第一个参数表示，把每个分区内的每个key的第一个value进行转化结构
第二个参数表示，每个分区内第一个参数进行转化后与分区内第二参数进行的计算规则
第三个参数表示，分区间数据的计算规则

#### 2.4.3.9 sortByKey()按照K进行排序
需求说明：创建一个pairRDD，按照key的正序和倒序进行排序
代码实现
```
object KeyValue07_sortByKey {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3具体业务逻辑
        //3.1 创建第一个RDD
        val rdd: RDD[(Int, String)] = sc.makeRDD(Array((3,"aa"),(6,"cc"),(2,"bb"),(1,"dd")))

        //3.2 按照key的正序（默认顺序）
        rdd.sortByKey(true).collect().foreach(println)

        //3.3 按照key的倒序
        rdd.sortByKey(false).collect().foreach(println)

        //4.关闭连接
        sc.stop()
    }
}
```

#### 2.4.3.10 mapValues()只对V进行操作
代码实现
```
object KeyValue08_mapValues {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3具体业务逻辑
        //3.1 创建第一个RDD
        val rdd: RDD[(Int, String)] = sc.makeRDD(Array((1, "a"), (1, "d"), (2, "b"), (3, "c")))

        //3.2 对value添加字符串"|||"
        rdd.mapValues(_ + "|||").collect().foreach(println)

        //4.关闭连接
        sc.stop()
    }
}
```

#### 2.4.3.11 join()连接 将相同key对应的多个value关联在一起
需求说明：创建两个pairRDD，并将key相同的数据聚合到一个元组。
代码实现
```
object KeyValue09_join {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3具体业务逻辑
        //3.1 创建第一个RDD
        val rdd: RDD[(Int, String)] = sc.makeRDD(Array((1, "a"), (2, "b"), (3, "c")))

        //3.2 创建第二个pairRDD
        val rdd1: RDD[(Int, Int)] = sc.makeRDD(Array((1, 4), (2, 5), (4, 6)))

        //3.3 join操作并打印结果
        rdd.join(rdd1).collect().foreach(println)

        //4.关闭连接
        sc.stop()
    }
}
```

#### 2.4.3.12 cogroup() 类似全连接，但是在同一个RDD中对key聚合
<font color=red>操作两个RDD中的KV元素，每个RDD中相同key中的元素分别聚合成一个集合。</font>
代码实现
```
object KeyValue10_cogroup {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3具体业务逻辑
        //3.1 创建第一个RDD
        val rdd: RDD[(Int, String)] = sc.makeRDD(Array((1,"a"),(2,"b"),(3,"c")))

        //3.2 创建第二个RDD
        val rdd1: RDD[(Int, Int)] = sc.makeRDD(Array((1,4),(2,5),(3,6)))

        //3.3 cogroup两个RDD并打印结果
        rdd.cogroup(rdd1).collect().foreach(println)

        //4.关闭连接
        sc.stop()
    }
}
```

## 2.5 Action 行动算子
行动算子是触发了整个作业的执行。因为转换算子都是懒加载，并不会立即执行。

### 2.5.1 reduce()聚合
1) 函数签名：def reduce(f: (T, T) => T): T
2) 功能说明：f函数聚集RDD中的所有元素，先聚合分区内数据，再聚合分区间数据。
3) 需求说明：创建一个RDD，将所有元素聚合得到结果

```
object action01_reduce {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3具体业务逻辑
        //3.1 创建第一个RDD
        val rdd: RDD[Int] = sc.makeRDD(List(1,2,3,4))

        //3.2 聚合数据
        val reduceResult: Int = rdd.reduce(_+_)
        println(reduceResult)

        //4.关闭连接
        sc.stop()
    }
}
```

### 2.5.2 collect()以数组的形式返回数据集
1) 函数签名：def collect(): Array[T]
2) 功能说明：在驱动程序中，以数组Array的形式返回数据集的所有元素。
<font color=red>注意：所有的数据都会被拉取到Driver端，慎用</font>
3) 需求说明：创建一个RDD，并将RDD内容收集到Driver端打印

```
object action02_collect {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3具体业务逻辑
        //3.1 创建第一个RDD
        val rdd: RDD[Int] = sc.makeRDD(List(1,2,3,4))

        //3.2 收集数据到Driver
        rdd.collect().foreach(println)

        //4.关闭连接
        sc.stop()
    }
}
```

### 2.5.3 count()返回RDD中元素个数
1) 函数签名：def count(): Long
2) 功能说明：返回RDD中元素的个数
3) 需求说明：创建一个RDD，统计该RDD的条数

```
object action03_count {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3具体业务逻辑
        //3.1 创建第一个RDD
        val rdd: RDD[Int] = sc.makeRDD(List(1,2,3,4))

        //3.2 返回RDD中元素的个数
        val countResult: Long = rdd.count()
        println(countResult)

        //4.关闭连接
        sc.stop()
    }
}
```

### 2.5.4 first()返回RDD中的第一个元素
1) 函数签名： def first(): T
2) 功能说明：返回RDD中的第一个元素
3) 需求说明：创建一个RDD，返回该RDD中的第一个元素

```
object action04_first {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3具体业务逻辑
        //3.1 创建第一个RDD
        val rdd: RDD[Int] = sc.makeRDD(List(1,2,3,4))

        //3.2 返回RDD中元素的个数
        val firstResult: Int = rdd.first()
        println(firstResult)

        //4.关闭连接
        sc.stop()
    }
}
```

### 2.5.5 take()返回由RDD前n个元素组成的数组
1) 函数签名： def take(num: Int): Array[T]
2) 功能说明：返回一个由RDD的前n个元素组成的数组
3) 需求说明：创建一个RDD，统计该RDD的条数

```
object action05_take {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3具体业务逻辑
        //3.1 创建第一个RDD
        val rdd: RDD[Int] = sc.makeRDD(List(1,2,3,4))

        //3.2 返回RDD中元素的个数
        val takeResult: Array[Int] = rdd.take(2)
        println(takeResult)

        //4.关闭连接
        sc.stop()
    }
}
```

### 2.5.6 takeOrdered()返回该RDD排序后前n个元素组成的数组
1) 函数签名： def takeOrdered(num: Int)(implicit ord: Ordering[T]): Array[T]
2) 功能说明：返回该RDD排序后的前n个元素组成的数组

```
def takeOrdered(num: Int)(implicit ord: Ordering[T]): Array[T] = withScope {
    ......
    if (mapRDDs.partitions.length == 0) {
        Array.empty
    } else {
        mapRDDs.reduce { (queue1, queue2) =>
          queue1 ++= queue2
          queue1
        }.toArray.sorted(ord)
    }
}
```
3) 需求说明：创建一个RDD，获取该RDD排序后的前2个元素

```
object action06_takeOrdered{

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3具体业务逻辑
        //3.1 创建第一个RDD
        val rdd: RDD[Int] = sc.makeRDD(List(1,3,2,4))

        //3.2 返回RDD中元素的个数
        val result: Array[Int] = rdd.takeOrdered(2)
        println(result)

        //4.关闭连接
        sc.stop()
    }
}
```

### 2.5.7 aggregate()案例
1) 函数签名: def aggregate[U: ClassTagl(zeroValue: U)(seqOp: (U, T) => U, combOp: (U, U) => U): U
2) 功能说明:aggregate函数将每个分区里面的元素通过分区内逻辑和初始值进行聚合，然后用分区间逻辑和初始值(zeroValue)进行操作。注意: 分区间逻辑再次使用初始值和aggregateByKey是有区别的。
3) 需求说明：创建一个RDD，将所有元素相加得到结果

```
object action07_aggregate {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3具体业务逻辑
        //3.1 创建第一个RDD
        val rdd: RDD[Int] = sc.makeRDD(List(1, 2, 3, 4),8)

        //3.2 将该RDD所有元素相加得到结果
        //val result: Int = rdd.aggregate(0)(_ + _, _ + _)
        val result: Int = rdd.aggregate(10)(_ + _, _ + _)

        println(result)

        //4.关闭连接
        sc.stop()
    }
}
```

### 2.5.8 fold()案例
1) 函数签名: def fold(zeroValue: T)(op:(T, T)=> T):T
2) 功能说明:折叠操作，aggregate的简化操作，即，分区内逻辑和分区间逻辑相同
3) 需求说明：创建一个RDD，将所有元素相加得到结果

```
object action08_fold {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3具体业务逻辑
        //3.1 创建第一个RDD
        val rdd: RDD[Int] = sc.makeRDD(List(1, 2, 3, 4))

        //3.2 将该RDD所有元素相加得到结果
        val foldResult: Int = rdd.fold(0)(_+_)
        println(foldResult)

        //4.关闭连接
        sc.stop()
    }
}
```

### 2.5.9 countByKey()统计每种key的个数
1) 函数签名：def countByKey(): Map[K, Long]
2) 功能说明：统计每种key的个数
3) 需求说明：创建一个PairRDD，统计每种key的个数

```
object action09_countByKey {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3具体业务逻辑
        //3.1 创建第一个RDD
        val rdd: RDD[(Int, String)] = sc.makeRDD(List((1, "a"), (1, "a"), (1, "a"), (2, "b"), (3, "c"), (3, "c")))

        //3.2 统计每种key的个数
        val result: collection.Map[Int, Long] = rdd.countByKey()
        println(result)

        //4.关闭连接
        sc.stop()
    }
}
```

### 2.5.10 save相关算子
1) saveAsTextFile(path)保存成Text文件

>1.函数签名
>2.功能说明：将数据集的元素以textfile的形式保存到HDFS文件系统或者其他支持的文件系统，对于每个元素，Spark将会调用toString方法，将它装换为文件中的文本

2) saveAsSequenceFile(path) 保存成Sequencefile文件

>1.函数签名
>2.功能说明：将数据集中的元素以Hadoop Sequencefile的格式保存到指定的目录下，可以使HDFS或者其他Hadoop支持的文件系统。
><font color=red>注意：只有kv类型RDD有该操作，单值的没有</font>

3) saveAsObjectFile(path) 序列化成对象保存到文件

>1.函数签名
>2.功能说明：用于将RDD中的元素序列化成对象，存储到文件中。

4) 代码实现

```
object action10_save {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3具体业务逻辑
        //3.1 创建第一个RDD
        val rdd: RDD[Int] = sc.makeRDD(List(1,2,3,4),2)

        //3.2 保存成Text文件
        rdd.saveAsTextFile("output")

        //3.3 序列化成对象保存到文件
        rdd.saveAsObjectFile("output1")

        //3.4 保存成Sequencefile文件
        rdd.map((_,1)).saveAsSequenceFile("output2")

        //4.关闭连接
        sc.stop()
    }
}
```

### 2.5.11 foreach(f)遍历RDD中每一个元素
1) 函数签名: defforeach(f:T=> Unit): Unit
2) 功能说明:遍历RDD中的每一个元素，并依次应用f函数
3) 需求说明：创建一个RDD，对每个元素进行打印

```
object action11_foreach {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3具体业务逻辑
        //3.1 创建第一个RDD
        // val rdd: RDD[Int] = sc.makeRDD(List(1,2,3,4),2)
        val rdd: RDD[Int] = sc.makeRDD(List(1,2,3,4))

        //3.2 收集后打印
        rdd.map(num=>num).collect().foreach(println)

        println("****************")
        //3.3 分布式打印
        rdd.foreach(println)

        //4.关闭连接
        sc.stop()
    }
}
```

## 2.6 RDD依赖关系

### 2.6.1 查看血缘关系
>RDD只支持粗粒度转换，即在大量记录上执行的单个操作。将创建RDD的一系列Lineage（血统）记录下来，以便恢复丢失的分区。RDD的Lineage会记录RDD的元数据信息和转换行为，当该RDD的部分分区数据丢失时，它可以根据这些信息来重新运算和恢复丢失的数据分区。

代码实现
```
object Lineage01 {

    def main(args: Array[String]): Unit = {
        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        val fileRDD: RDD[String] = sc.textFile("input/1.txt")
        println(fileRDD.toDebugString)
        println("----------------------")

        val wordRDD: RDD[String] = fileRDD.flatMap(_.split(" "))
        println(wordRDD.toDebugString)
        println("----------------------")

        val mapRDD: RDD[(String, Int)] = wordRDD.map((_,1))
        println(mapRDD.toDebugString)
        println("----------------------")

        val resultRDD: RDD[(String, Int)] = mapRDD.reduceByKey(_+_)
        println(resultRDD.toDebugString)

        resultRDD.collect()

        //4.关闭连接
        sc.stop()
    }
}
```
打印结果
```
(2) input/1.txt MapPartitionsRDD[1] at textFile at Lineage01.scala:15 []
 |  input/1.txt HadoopRDD[0] at textFile at Lineage01.scala:15 []
----------------------
(2) MapPartitionsRDD[2] at flatMap at Lineage01.scala:19 []
 |  input/1.txt MapPartitionsRDD[1] at textFile at Lineage01.scala:15 []
 |  input/1.txt HadoopRDD[0] at textFile at Lineage01.scala:15 []
----------------------
(2) MapPartitionsRDD[3] at map at Lineage01.scala:23 []
 |  MapPartitionsRDD[2] at flatMap at Lineage01.scala:19 []
 |  input/1.txt MapPartitionsRDD[1] at textFile at Lineage01.scala:15 []
 |  input/1.txt HadoopRDD[0] at textFile at Lineage01.scala:15 []
----------------------
(2) ShuffledRDD[4] at reduceByKey at Lineage01.scala:27 []
 +-(2) MapPartitionsRDD[3] at map at Lineage01.scala:23 []
    |  MapPartitionsRDD[2] at flatMap at Lineage01.scala:19 []
    |  input/1.txt MapPartitionsRDD[1] at textFile at Lineage01.scala:15 []
    |  input/1.txt HadoopRDD[0] at textFile at Lineage01.scala:15 []
```
<font color=red>注意：圆括号中的数字表示RDD的并行度，也就是有几个分区</font>

### 2.6.2 查看依赖关系
代码实现
```
object Lineage01 {

    def main(args: Array[String]): Unit = {
        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        val fileRDD: RDD[String] = sc.textFile("input/1.txt")
        println(fileRDD.dependencies)
        println("----------------------")

        val wordRDD: RDD[String] = fileRDD.flatMap(_.split(" "))
        println(wordRDD.dependencies)
        println("----------------------")

        val mapRDD: RDD[(String, Int)] = wordRDD.map((_,1))
        println(mapRDD.dependencies)
        println("----------------------")

        val resultRDD: RDD[(String, Int)] = mapRDD.reduceByKey(_+_)
        println(resultRDD.dependencies)

        resultRDD.collect()

        //4.关闭连接
        sc.stop()
    }
}
```
打印结果
```
List(org.apache.spark.OneToOneDependency@f2ce6b)
----------------------
List(org.apache.spark.OneToOneDependency@692fd26)
----------------------
List(org.apache.spark.OneToOneDependency@627d8516)
----------------------
List(org.apache.spark.ShuffleDependency@a518813)
```
全局搜索org.apache.spark.OneToOneDependency
```
class OneToOneDependency[T](rdd: RDD[T]) extends NarrowDependency[T](rdd) {
    override def getParents(partitionId: Int): List[Int] = List(partitionId)
}
```
><font color=red>注意：要想理解RDDS是如何工作的，最重要的就是理解Transformations。
RDD 之间的关系可以从两个维度来理解: 一个是 RDD 是从哪些 RDD 转换而来, 也就是 RDD 的 parent RDD(s)是什么; 另一个就是 RDD 依赖于 parent RDD(s)的哪些 Partition(s). 这种关系就是 RDD 之间的依赖
</font>

>RDD和它依赖的父RDD（s）的关系有两种不同的类型，即窄依赖（narrow dependency）和宽依赖（wide dependency）

### 2.6.3 窄依赖
窄依赖表示每一个父RDD的Partition最多被子RDD的一个Partition使用，窄依赖我们形象的比喻为独生子女。

### 2.6.4 宽依赖
宽依赖表示同一个父RDD的Partition被多个子RDD的Partition依赖，会引起Shuffle，总结：宽依赖我们形象的比喻为超生。
><font color=red>具有宽依赖的 transformations 包括: sort, reduceByKey, groupByKey, join, 和调用rePartition函数的任何操作.</font>

><font color=red>宽依赖对 Spark 去评估一个 transformations 有更加重要的影响, 比如对性能的影响</font>

### 2.6.5 Stage任务划分
>1) DAG有向无环图
DAG（Directed Acyclic Graph）有向无环图是由点和线组成的拓扑图形，该图形具有方向，不会闭环。例如，DAG记录了RDD的转换过程和任务的阶段。

>2) 任务运行的整体流程

>3) RDD任务切分中间分为：Application、Job、Stage和Task
Application：初始化一个SparkContext即生成一个Application；
Job：一个Action算子就会生成一个Job；
Stage：Stage等于宽依赖的个数加1；
Task：一个Stage阶段中，最后一个RDD的分区个数就是Task的个数。
<font color=red>注意：Application->Job->Stage->Task每一层都是1对n的关系。</font>

>4) 代码实现
```
object Stage01 {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2. Application：初始化一个SparkContext即生成一个Application；
        val sc: SparkContext = new SparkContext(conf)

        //3. 创建RDD
        val dataRDD: RDD[Int] = sc.makeRDD(List(1,2,3,4,1,2),2)

        //3.1 聚合
        val resultRDD: RDD[(Int, Int)] = dataRDD.map((_,1)).reduceByKey(_+_)

        // Job：一个Action算子就会生成一个Job；
        //3.2 job1打印到控制台
        resultRDD.collect().foreach(println)

        //3.3 job2输出到磁盘
        resultRDD.saveAsTextFile("output")

        Thread.sleep(1000000)

        //4.关闭连接
        sc.stop()
    }
}
```
>5) 查看Job个数
查看http://localhost:4040/jobs/，发现Job有两个。

>6) 查看Stage个数
查看Job0的Stage。由于只有1个Shuffle阶段，所以Stage个数为2。
查看Job1的Stage。由于只有1个Shuffle阶段，所以Stage个数为2。

>7) Task个数
查看Job0的Stage0的Task个数
查看Job0的Stage1的Task个数
查看Job1的Stage2的Task个数
查看Job1的Stage3的Task个数
<font color=red>注意：如果存在shuffle过程，系统会自动进行缓存，UI界面显示skipped的部分</font>

## RDD持久化

### 2.7.1 RDD Cache缓存
RDD通过Cache或者Persist方法将前面的计算结果缓存，默认情况下会把数据以序列化的形式缓存在JVM的堆内存中。但是并不是这两个方法被调用时立即缓存，而是触发后面的action时，该RDD将会被缓存在计算节点的内存中，并供后面重用。
1) 代码实现

```
object cache01 {

    def main(args: Array[String]): Unit = {
        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3. 创建一个RDD，读取指定位置文件:hello atguigu atguigu
        val lineRdd: RDD[String] = sc.textFile("input1")

        //3.1.业务逻辑
        val wordRdd: RDD[String] = lineRdd.flatMap(line => line.split(" "))

        val wordToOneRdd: RDD[(String, Int)] = wordRdd.map {
            word => {
                println("************")
                (word, 1)
            }
        }

        //3.5 cache操作会增加血缘关系，不改变原有的血缘关系
        println(wordToOneRdd.toDebugString)

        //3.4 数据缓存。
        wordToOneRdd.cache()
        //3.6 可以更改存储级别
        // wordToOneRdd.persist(StorageLevel.MEMORY_AND_DISK_2)

        //3.2 触发执行逻辑
        wordToOneRdd.collect()

        println("-----------------")
        println(wordToOneRdd.toDebugString)

        //3.3 再次触发执行逻辑
        wordToOneRdd.collect()

        //4.关闭连接
        sc.stop()
    }
}
```
2) 源码解析

```
mapRdd.cache()
def cache(): this.type = persist()
def persist(): this.type = persist(StorageLevel.MEMORY_ONLY)

object StorageLevel {
  val NONE = new StorageLevel(false, false, false, false)
  val DISK_ONLY = new StorageLevel(true, false, false, false)
  val DISK_ONLY_2 = new StorageLevel(true, false, false, false, 2)
  val MEMORY_ONLY = new StorageLevel(false, true, false, true)
  val MEMORY_ONLY_2 = new StorageLevel(false, true, false, true, 2)
  val MEMORY_ONLY_SER = new StorageLevel(false, true, false, false)
  val MEMORY_ONLY_SER_2 = new StorageLevel(false, true, false, false, 2)
  val MEMORY_AND_DISK = new StorageLevel(true, true, false, true)
  val MEMORY_AND_DISK_2 = new StorageLevel(true, true, false, true, 2)
  val MEMORY_AND_DISK_SER = new StorageLevel(true, true, false, false)
  val MEMORY_AND_DISK_SER_2 = new StorageLevel(true, true, false, false, 2)
  val OFF_HEAP = new StorageLevel(true, true, true, false, 1)
```
<font color=red>注意：默认的存储级别都是仅在内存存储一份。在存储级别的末尾加上“_2”表示持久化的数据存为两份。</font>
>缓存有可能丢失，或者存储于内存的数据由于内存不足而被删除，RDD的缓存容错机制保证了即使缓存丢失也能保证计算的正确执行。通过基于RDD的一系列转换，丢失的数据会被重算，由于RDD的各个Partition是相对独立的，因此只需要计算丢失的部分即可，并不需要重算全部Partition。

3) 自带缓存算子

>Spark会自动对一些Shuffle操作的中间数据做持久化操作(比如：reduceByKey)。这样做的目的是为了当一个节点Shuffle失败了避免重新计算整个输入。但是，在实际使用的时候，如果想重用数据，仍然建议调用persist或cache。
```
object cache02 {

    def main(args: Array[String]): Unit = {
        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3. 创建一个RDD，读取指定位置文件:hello atguigu atguigu
        val lineRdd: RDD[String] = sc.textFile("input1")

        //3.1.业务逻辑
        val wordRdd: RDD[String] = lineRdd.flatMap(line => line.split(" "))

        val wordToOneRdd: RDD[(String, Int)] = wordRdd.map {
            word => {
                println("************")
                (word, 1)
            }
        }

        // 采用reduceByKey，自带缓存
        val wordByKeyRDD: RDD[(String, Int)] = wordToOneRdd.reduceByKey(_+_)

        //3.5 cache操作会增加血缘关系，不改变原有的血缘关系
        println(wordByKeyRDD.toDebugString)

        //3.4 数据缓存。
        //wordByKeyRDD.cache()

        //3.2 触发执行逻辑
        wordByKeyRDD.collect()

        println("-----------------")
        println(wordByKeyRDD.toDebugString)

        //3.3 再次触发执行逻辑
        wordByKeyRDD.collect()

        //4.关闭连接
        sc.stop()
    }
}
```
访问http://localhost:4040/jobs/页面，查看第一个和第二个job的DAG图。说明：增加缓存后血缘依赖关系仍然有，但是，第二个job取的数据是从缓存中取的。


### 2.7.2 RDD CheckPoint检查点
>1)检查点：是通过将RDD中间结果写入磁盘。

>2)为什么要做检查点？
  由于血缘依赖过长会造成容错成本过高，这样就不如在中间阶段做检查点容错，如果检查点之后有节点出现问题，可以从检查点开始重做血缘，减少了开销。

>3)检查点存储路径：Checkpoint的数据通常是存储在HDFS等容错、高可用的文件系统

>4)检查点数据存储格式为：二进制的文件

>5)检查点切断血缘：在Checkpoint的过程中，该RDD的所有依赖于父RDD中的信息将全部被移除。

>6)检查点触发时间：对RDD进行checkpoint操作并不会马上被执行，必须执行Action操作才能触发。但是检查点为了数据安全，会从血缘关系的最开始执行一遍

>7)设置检查点步骤
   设置检查点数据存储路径：sc.setCheckpointDir("./checkpoint1")
   调用检查点方法：wordToOneRdd.checkpoint()

>8)代码实现
```
object checkpoint01 {

    def main(args: Array[String]): Unit = {
        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        // 需要设置路径，否则抛异常：Checkpoint directory has not been set in the SparkContext
        sc.setCheckpointDir("./checkpoint1")

        //3. 创建一个RDD，读取指定位置文件:hello atguigu atguigu
        val lineRdd: RDD[String] = sc.textFile("input1")

        //3.1.业务逻辑
        val wordRdd: RDD[String] = lineRdd.flatMap(line => line.split(" "))

        val wordToOneRdd: RDD[(String, Long)] = wordRdd.map {
            word => {
                (word, System.currentTimeMillis())
            }
        }

        //3.5 增加缓存,避免再重新跑一个job做checkpoint
//        wordToOneRdd.cache()

        //3.4 数据检查点：针对wordToOneRdd做检查点计算
        wordToOneRdd.checkpoint()

        //3.2 触发执行逻辑
        wordToOneRdd.collect().foreach(println)
        // 会立即启动一个新的job来专门的做checkpoint运算

        //3.3 再次触发执行逻辑
        wordToOneRdd.collect().foreach(println)
        wordToOneRdd.collect().foreach(println)

        Thread.sleep(10000000)

        //4.关闭连接
        sc.stop()
    }
}
```
>9) 执行结果
  访问http://localhost:4040/jobs/页面，查看4个job的DAG图。其中第2个图是checkpoint的job运行DAG图。第3、4张图说明，检查点切断了血缘依赖关系。
  （1）只增加checkpoint，没有增加Cache缓存打印
  第1个job执行完，触发了checkpoint，第2个job运行checkpoint，并把数据存储在检查点上。第3、4个job，数据从检查点上直接读取。
```
(hadoop,1577960215526)
。。。。。。
(hello,1577960215526)
(hadoop,1577960215609)
。。。。。。
(hello,1577960215609)
(hadoop,1577960215609)
。。。。。。
(hello,1577960215609)
```
>（2）增加checkpoint，也增加Cache缓存打印
	第1个job执行完，数据就保存到Cache里面了，第2个job运行checkpoint，直接读取Cache里面的数据，并把数据存储在检查点上。第3、4个job，数据从检查点上直接读取。
```
(hadoop,1577960642223)
。。。。。。
(hello,1577960642225)
(hadoop,1577960642223)
。。。。。。
(hello,1577960642225)
(hadoop,1577960642223)
。。。。。。
(hello,1577960642225)
```

### 2.7.3 缓存和检查点区别
1) Cache缓存只是将数据保存起来，不切断血缘依赖。Checkpoint检查点切断血缘依赖。
2) Cache缓存的数据通常存储在磁盘、内存等地方，可靠性低。Checkpoint的数据通常存储在HDFS等容错、高可用的文件系统，可靠性高。
3) 建议对checkpoint()的RDD使用Cache缓存，这样checkpoint的job只需从Cache缓存中读取数据即可，否则需要再从头计算一次RDD。
4) 如果使用完了缓存，可以通过unpersist（）方法释放缓存

###2.7.4 检查点存储到HDFS集群
如果检查点数据存储到HDFS集群，要注意配置访问集群的用户名。否则会报访问权限异常。
```
object checkpoint02 {

    def main(args: Array[String]): Unit = {

        // 设置访问HDFS集群的用户名
        System.setProperty("HADOOP_USER_NAME","atguigu")

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        // 需要设置路径.需要提前在HDFS集群上创建/checkpoint路径
        sc.setCheckpointDir("hdfs://hadoop102:9000/checkpoint")

        //3. 创建一个RDD，读取指定位置文件:hello atguigu atguigu
        val lineRdd: RDD[String] = sc.textFile("input1")

        //3.1.业务逻辑
        val wordRdd: RDD[String] = lineRdd.flatMap(line => line.split(" "))

        val wordToOneRdd: RDD[(String, Long)] = wordRdd.map {
            word => {
                (word, System.currentTimeMillis())
            }
        }

        //3.4 增加缓存,避免再重新跑一个job做checkpoint
        wordToOneRdd.cache()

        //3.3 数据检查点：针对wordToOneRdd做检查点计算
        wordToOneRdd.checkpoint()

        //3.2 触发执行逻辑
        wordToOneRdd.collect().foreach(println)

        //4.关闭连接
        sc.stop()
    }
```

## 3.1 文件类数据读取与保存
Spark的数据读取及数据保存可以从两个维度来作区分：文件格式以及文件系统。
文件格式分为：Text文件、Json文件、Csv文件、Sequence文件以及Object文件；
文件系统分为：本地文件系统、HDFS以及数据库。

### 3.1.1 Text文件
1) 数据读取：textFile(String)
2) 数据保存：saveAsTextFile(String)
3) 代码实现
com.userl.readAndSave

```
object Operate_Text {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[1]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3.1 读取输入文件
        val inputRDD: RDD[String] = sc.textFile("input/1.txt")

        //3.2 保存数据
        inputRDD.saveAsTextFile("output")

        //4.关闭连接
        sc.stop()
    }
}
```
4) 注意：如果是集群路径：hdfs://hadoop102:9000/input/1.txt

### 3.1.2 Sequence文件
SequenceFile文件是Hadoop用来存储二进制形式的key-value对而设计的一种平面文件(Flat File)。在SparkContext中，可以调用sequenceFile[keyClass, valueClass](path)。
1) 代码实现

```
object Operate_Sequence {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[1]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3.1 创建rdd
        val dataRDD: RDD[(Int, Int)] = sc.makeRDD(Array((1,2),(3,4),(5,6)))

        //3.2 保存数据为SequenceFile
        dataRDD.saveAsSequenceFile("output")

        //3.3 读取SequenceFile文件
        sc.sequenceFile[Int,Int]("output").collect().foreach(println)

        //4.关闭连接
        sc.stop()
    }
}
```
2) <font color=red>注意：SequenceFile文件只针对PairRDD</font>

### 3.1.3 Object对象文件
对象文件是将对象序列化后保存的文件，采用Java的序列化机制。可以通过objectFile[k,v](path)函数接收一个路径，读取对象文件，返回对应的RDD，也可以通过调用saveAsObjectFile()实现对对象文件的输出。因为是序列化所以要指定类型。
1) 代码实现
```
object Operate_Object {

    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[1]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3.1 创建RDD
        val dataRDD: RDD[Int] = sc.makeRDD(Array(1,2,3,4))

        //3.2 保存数据
        dataRDD.saveAsObjectFile("output")

        //3.3 读取数据
        sc.objectFile[(Int)]("output").collect().foreach(println)

        //4.关闭连接
        sc.stop()
    }
}
```

