---
title: spark入门笔记
date: 2021-03-05 16:54:36
tags:
---

最近居然有了一点期末前预习功课的感觉，脑容量不足，做一些记录。

### 安装spark
Spark需要JDK1.8或更高版本的支持。

下载[Apache spark-3.0.2](http://archive.apache.org/dist/spark/spark-3.0.2/spark-3.0.2-bin-hadoop3.2.tgz)上传至 *$HOME/software*。解压之后将 software/spark-3.0.2-bin-hadoop3.2/bin/ 添加至环境变量。

也尝试了conda安装pyspark，不过暂时还未使用。

### 利用sbt打包scala程序

sbt（Simple Build Tool）是对Scala或Java语言进行编译的一个工具，用于scala程序的打包。

#### sbt安装

下载 [sbt 1.4.7](https://github-releases.githubusercontent.com/279553/0319d680-635f-11eb-9d64-8506ad279378?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20210303%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20210303T075717Z&X-Amz-Expires=300&X-Amz-Signature=b8e2ea944b641d22d75c4cca2f371050654e08ce4da5aaeb80d5e9501842e714&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=279553&response-content-disposition=attachment%3B%20filename%3Dsbt-1.4.7.tgz&response-content-type=application%2Foctet-stream) 并上传至 *$HOME/software*。在安装目录下新建一个Shell脚本 ./sbt 并赋予可执行权限，用来启动sbt。同时将 *$HOME/software/sbt* 添加至换变量。脚本如下：

```bash
#!/bin/bash
SBT_OPTS="-Xms512M -Xmx1536M -Xss1M -XX:+CMSClassUnloadingEnabled -XX:MaxPermSize=256M"
java $SBT_OPTS -jar bin/sbt-launch.jar "$@"
```

查看sbt版本信息，第一次运行需要**联网**，下载依赖包。稍等几分钟之后就能看到版本信息了。

```bash
$ ./sbt sbtVersion
```

### 编写scala程序

新建 *01_spark_scripts* 文件夹用于存放之后的scala脚本。现在开始第一个scala程序。

```bash
$ cd 01_spark_scripts
$ mkdir -p 01_wordcount/src/main/scala
$ cd 01_wordcount
```

在 *01_wordcount* 下新建 word.txt 作为单词统计输入文件。在 *01_wordcount/src/main/scala* 下新建 wordcount.scala文件。

```java
object WordCount {
    def main(args: Array[String]) {
        val inputFile =  "$HOME/01_spark_scripts/01_workcount/word.txt"
        val conf = new SparkConf().setAppName("WordCount").setMaster("local[2]")
        val sc = new SparkContext(conf)
        val textFile = sc.textFile(inputFile)
        val wordCount = textFile.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey((a, b) => a + b)
        wordCount.foreach(println)
    }
}
```

### 用sbt打包scala程序

在 *01_wordcount* 下新建文件 simple.sbt 声明程序信息，添加内容如下：

```java
name := "WordCount"
version := "1.0"
scalaVersion := "2.12.10"
libraryDependencies += "org.apache.spark" %% "spark-core" % "3.0.2"
```

至此我们已经完成了打包之前的所有准备，可以检查一下文件夹结构：

```bash
$ find .
./simple.sbt
./src
./src/main
./src/main/scala
./src/main/scala/wordcount.scala
./word.txt
```

接着，我们就可以将整个应用程序打包成JAR，第一次运行sbt同样需要**联网**，下载依赖包。然后通过spark-submit提交运行。。

```bash
$ sbt package
$ spark-submit --class "WordCount" ./target/scala-2.12/wordcount_2.12-1.0.jar >count.txt 2> runlog
```

