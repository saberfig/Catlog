# 2020.05.13
今天是中期检查，不过我们分配在了下周，这节课我们就讨论了一下各自在编程的时候遇到的问题。

我遇到的问题如下：
1. 如何把多个表的查询结果合并成一个表（单纯的列合并，不是union）

解决办法：
### 把多个表的查询结果合并成一个表（单纯的列合并，不是union）
使用


# 2020.09.02

## 阿里云-Ubuntu安装MySQL8.0并配置

### 安装MySQL

```shell
sudo apt-get update
sudo apt-get install mysql-server
sudo netstat -tap | grep mysql //验证成功性
```

### 配置MySQL文件

编辑 /etc/mysql/mysql.conf.d/mysqld.cnf 文件
注释掉 bind-address = 127.0.0.1

### 创建用户并授权

```sql
create user 'new'@'%' identified by '123456z!';
grant all on program.* to 'new'@'%';
```

```sql
flush privileges;
```
```shell
sudo service mysql restart
```

### 阿里云实例配置安全组

阿里云服务器需要在安全组中开放端口，外网才能访问该端口
添加3306端口

## 数据库迁移

### 导出数据

```sql
mysqldump -h47.94.147.XXX -uroot -p --default-character-set=utf8 program > program.sql;
```

* -h后跟原数据库ip地址
* -r后跟原数据库用户名
* ```>``` 符号前跟数据库名
* ```>``` 符号后跟目标文件名

### 恢复数据

```sql
mysql newprogram < program.sql;
```

# 2020.09.03

## 一、云服务器部署Hadoop 伪分布式，本地虚拟机配置eclipse的Hadoop开发环境

### 0.参考文档
Ubuntu16.04 下 hadoop的安装与配置（伪分布式环境） https://www.cnblogs.com/87hbteo/p/7606012.html

最详细云服务器Centos7下搭建Hadoop伪分布式集群(防各种坑) https://www.jianshu.com/p/59fe6cdba293

> 伪分布式的部署以参考文档1为主  
> eclipse的Hadoop环境配置以参考文档2为主  
> 以下添加一些补充说明

### 1.Hadoop版本选择的重要性

因为Hadoop是部署在云服务器上的，需要通过公网访问，因此版本一致性会很重要。后面会配置eclipse的Hadoop开发环境，使用的插件版本是2.8.3，因此Hadoop我使用的也是2.8.3版本（第一次部署使用的是2.10.0，一直没有成功）

### 2.伪分布式的配置

核心修改的文件是core-site.xml和hdfs-site.xml

#### core-site.xml

```xml

<configuration>
    <property>
    <!--指定hadoop运行时产生文件的存放目录，默认放在/tmp中，每次重启会被删除-->
        <name>hadoop.tmp.dir</name>
        <value>file:/usr/local/hadoop/tmp</value>
    </property>
    <property>
    <!--指定namenode的地址-->
        <name>fs.defaultFS</name>
        <value>hdfs://hostname:9000</value>
    </property>
</configuration>

```

#### hdfs-site.xml

```xml

<configuration>
    <property>
    <!--因为配置的伪分布式只有一个datanode，所以值要置为1-->
        <name>dfs.replication</name>
        <value>1</value>
    </property>
    <property>
    <!--dfs-client使用域名-->
        <name>dfs.client.use.datanode.hostname</name>
        <value>true</value>
    </property>
    <property>
    <!--datanode使用域名-->
        <name>dfs.datanode.use.datanode.hostname</name>
        <value>true</value>
    </property>
    <property>
    <!--允许外部访问-->
      <name>dfs.permissions</name>
      <value>false</value>
    </property>
    <property>
        <name>dfs.namenode.name.dir</name>
        <value>file:/usr/local/hadoop/tmp/dfs/name</value>
    </property>
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>file:/usr/local/hadoop/tmp/dfs/data</value>
    </property>
</configuration>

```

### 3.使用域名访问而非ip访问

在本地电脑上访问远程服务器的hdfs会有两类通信：本地电脑通过公网访问远程服务器的namenode，namenode通过内网与datanode通信。

配置hosts文件使用域名访问会减少一些问题

#### hosts配置

hosts文件位置 ```/etc/hosts```

> 部署了Hadoop的云服务器：

* 添加 本机私网ip 主机名

> 安装eclipse的本地主机：

* 添加 云服务器公网ip 主机名

### 4.其他内容

#### 配置hadoop常用的端口

https://blog.csdn.net/wulantian/article/details/46341043

50070、8088
50010、50020、9000

#### hdfs常用命令

初识HDFS（10分钟了解HDFS、NameNode和DataNode）
https://www.cnblogs.com/wxplmm/p/7239342.html

hdfs常见命令
https://www.cnblogs.com/oyjg/p/11150891.html


## 二、本地主机配置eclipse的Hadoop开发环境（描述步骤）

### 1.安装hadoop

解压安装包即可，这里主要使用安装包中的MapReduce开发库，不会部署hadoop环境

### 2.添加hadoop插件

网上下载相关插件(jar包)放入eclipse的plugins文件夹中即可

重启eclipse或者虚拟机，打开Window->Perferences->Hadoop Map/Reduce 添加hadoop解压包的路径（之后可以直接创建Map/Reduce项目）

> 如果打开后没有找到Hadoop Map/Reduce，可能的原因是下载的插件有问题，重新找一个插件（我试了3个插件才成功，难顶）

### 3.配置hdfs

步骤2成功后可以在右下角看到一个小象的标志，打开后就能配置DFS Locations（整理桌面时，添加图片）

Map/Reduce Master指的是namenode，Host为云服务器公网ip，Port为50020（默认端口）

DFSMaster指的是HDFS节点 默认端口是9000

### 4.需要注意的两个点

#### 不要管Advanced parameters

网上有的教程有的设置Hadoop location的Advanced parameter的一些选项，但是我配置了很多，运行程序时，本地主机访问namenode还是会使用云服务器的内网ip


> 报错

```
WARN [hadoop.hdfs.BlockReaderFactory] I/O error constructing remote block reader.
java.net.ConnectException: Connection refused

WARN [hadoop.hdfs.DFSClient] Failed to connect to 172.17.189.191:50010 for block, add to deadNodes and continue. java.net.ConnectException: Connection refused
java.net.ConnectException: Connection refused
```

比较好的方法是复制云服务器上的配置文件```core-site.xml```和```hdfs-site.xml```两个配置文件到src文件夹下（如何创建任意名字文件，右键 new > other > General/File）

#### 运行程序时终端不打印信息

> 报错

```

log4j:WARN No appenders could be found for logger (org.apache.hadoop.metrics2.lib.MutableMetricsFactory).
log4j:WARN Please initialize the log4j system properly.
log4j:WARN See http://logging.apache.org/log4j/1.2/faq.html#noconfig for more info.

```

在src文件夹下创建log4j.properties文件

内容如下

```

log4j.rootLogger=INFO, stdout
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d %p [%c] - %m%n
log4j.appender.logfile=org.apache.log4j.FileAppender
log4j.appender.logfile.File=target/spring.log
log4j.appender.logfile.layout=org.apache.log4j.PatternLayout
log4j.appender.logfile.layout.ConversionPattern=%d %p [%c] - %m%n

```

## 三、MapReduce项目实践

### 1.hadoop工具包项目实践

Hadoop第一个样例Wordcount运行笔记
https://hijiangtao.github.io/2014/02/19/wordcountrunning/

### 2.eclipse项目实践（复制源码就行）

第一个MapReduce程序——WordCount（多文件）
https://songlee24.github.io/2015/07/29/mapreduce-word-count/

eclipse远程调试Hadoop（单文件）
https://www.cnblogs.com/cstzhou/p/5495434.html

Eclipse中使用Hadoop插件编写Map/Reduce程序（多文件，本地，jar包都有）
https://my.oschina.net/u/3049601/blog/1812221

### 3.运行参数说明

#### 程序外部参数

```
FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
```
这种形式的语句，需要配置项目参数

Run Configurations -> Arguments 添加输入和输出参数,参数是hdfs的路径

```
hdfs://公网ip:9000/input
hdfs://公网ip:9000/output
```
#### 程序内部参数
```
FileInputFormat.addInputPath(job, new Path("hdfs://公网ip:9000/input"));
FileOutputFormat.setOutputPath(job, new Path("hdfs://公网ip:9000/output"));
```
这种形式的就不用配置了

## 四、遇到的其他问题

### 1.总是处于safemode-安全模式

这个问题我没有解决，网上给出的原因都是磁盘空间不足导致的，但是我的磁盘剩余空间还很大，即使执行强制退出命令你个也没有效果，最终我从2.10.0版本，换到2.8.3版本，这个问题就没再出现了

#### 补充，关于safemode的一些命令
```c
hdfs dfsadmin -safemode get //查看是否处于安全模式
hdfs dfsadmin -safemode leave //强制退出安全模式 
```
































