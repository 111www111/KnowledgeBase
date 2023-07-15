# Flume

此处附上 flume学习代码  github地址：https://github.com/111www111/wyt-flume

## 一、概述

### 1.Flume定义

> Flume是Cloudera提供的一个海量日志采集、传输的系统。Flume基于流式架构，灵活简单。

![](images/WEBRESOURCEc315fba31d57b68e7e211a54be4416e2.octet-stream)

### 2.Flume优点

> ① 可以和任意存储进程集成

> ② 输入的数据速率大于写入目的存储的速率，flume会进行缓冲，减小hdfs的压力。

> ③ flume中的事务基于channel，使用了两个事务模型（sender + receiver），确保消息被可靠发送。​      	   

> 

> Flume使用两个独立的事务分别负责从source到channel，以及从channel到sink的事件传递。一旦事务中所有的数据全部成功提交到channel，那么source才认为该数据读取完成。同理，只有成功被sink写出去的数据，才会从channel中移除。



### 3.Flume组成架构

![](images/WEBRESOURCE31a48236097a7df5aeee23a7c144db7a.octet-stream)



### 4.Flume组件

- Agent

> Agent是一个JVM进程，它以事件的形式将数据从源头送至目的地。Agent主要有3个部分组成，Source、Channel、Sink。

- Source

> Source是负责接收数据到Flume Agent的组件。

- Channel

> Channel是位于Source和Sink之间的缓冲区。因此，Channel允许Source和Sink运作在不同的速率上。Channel是线程安全的，可以同时处理几个Source的写入操作和几个Sink的读取操作。Flume自带两种Channel：Memory Channel和File Channel。Memory Channel是内存中的队列。Memory Channel在不需要关心数据丢失的情景下适用。如果需要关心数据丢失，那么Memory Channel就不应该使用，因为程序死亡、机器宕机或者重启都会导致数据丢失。File Channel将所有事件写到磁盘。因此在程序关闭或机器宕机的情况下不会丢失数据。

- Sink

> Sink不断地轮询Channel中的事件且批量地移除它们，并将这些事件批量写入到存储或索引系统、或者被发送到另一个Flume Agent。

- Event (事件  -->  新的产生数据)

> 传输单元，Flume数据传输的基本单元，以事件的形式将数据从源头送至目的地。  Event由可选的header和载有数据的一个byte array 构成。Header是容纳了key-value字符串对的HashMap。

![](images/WEBRESOURCE1af87d53b1996bf8a0725a62eeb3c773.octet-stream)

## 二、安装

### 1.安装地址

1） Flume官网地址

http://flume.apache.org

2）文档查看地址

http://flume.apache.org/FlumeUserGuide.html

3）下载地址

http://archive.apache.org/dist/flume/

### 2.安装步骤

准备工作：安装JDK、并且配置环境变量

1）将apache-flume-1.9.0-bin.tar.gz上传到linux的/opt/modules目录下

2）解压apache-flume-1.9.0-bin.tar.gz到/opt/installs目录下

3）将flume/conf下的flume-env.sh.template文件修改为flume-env.sh，并配置flume-env.sh文件

```
[root@flume0 conf]# pwd
/opt/installs/apache-flume-1.9.0-bin/conf
[root@flume0 conf]# mv flume-env.sh.template flume-env.sh
[root@flume0 conf]# vi flume-env.sh
export JAVA_HOME=/opt/installs/jdk1.8
​
[root@flume0 apache-flume-1.9.0-bin]# bin/flume-ng version
Flume 1.9.0
Source code repository: https://git-wip-us.apache.org/repos/asf/flume.git
Revision: d4fcab4f501d41597bc616921329a4339f73585e
Compiled by fszabo on Mon Dec 17 20:45:25 CET 2018
From source with checksum 35db629a3bda49d23e9b3690c80737f9
```

## Flume快速入门

1. 在Hadoop10机器里面安装netcat

```
yum install -y nc
```

1. 到flume官方网站中复制配置信息

https://flume.apache.org/releases/content/1.9.0/FlumeUserGuide.html

```
# Name部分，命名组件名称 a1是当前agent的名字 
a1.sources = r1
a1.sinks = k1
a1.channels = c1
​
# 这是source 的描述信息 监控的数据来源（类型 IP 端口）
a1.sources.r1.type = netcat
a1.sources.r1.bind = localhost
a1.sources.r1.port = 44444
​
# 这是 sink的类型
a1.sinks.k1.type = logger
​
# 这是channel的类型，容量，事务容量
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100
​
# 将来源与清洗池绑定信道
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```

1. 要flume的jobs文件下创建要给文件，文件名是first.conf。 把从官方网站中复制的内容粘贴到这个文件里面

1. 执行flume的bin目录下的agent-ng，查看对应的参数信息

> ./agent-ng help通过help实现帮助信息的查看，可以看到agent-ng里面可以写的命令以及对应的参数信息

1. 在flume的根目录下，执行bin目录下的agent-ng,同时携带参数

```

bin/flume-ng agent -n a1 -c conf -f jobs/first.conf -flume.root.logger=INFO,console
```

1. 在另外的一个窗口中，监听44444端口号，同时实现数据的发送

```

nc localhost 44444
```

1. 到启动flume agent的界面查看是否显示了对应的发送信息

## 三、企业开发案例

### 1.实时读取目录文件到HDFS案例

1）案例需求：使用Flume监听整个目录的文件

2）需求分析：

![](images/WEBRESOURCE08b27827847786925ae255df99e35113.octet-stream)

3）实现步骤：

1. 创建spooldir-memory-hdfs.conf

```
[root@flume0 job]# touch spooldir-memory-hdfs.conf
​
# 内容如下
a1.sources = r1
a1.channels = c1
a1.sinks = k1
​
a1.sources.r1.type = spooldir
a1.sources.r1.spoolDir = /opt/upload
a1.sources.r1.fileSuffix = .done
​
a1.channels.c1.type = memory
​
a1.sinks.k1.type = hdfs
a1.sinks.k1.hdfs.path = hdfs://flume0:9000/flume/%Y-%m-%d/%H
a1.sinks.k1.hdfs.useLocalTimeStamp = true
#设置文件类型
a1.sinks.k1.hdfs.fileType = DataStream
​
#是否按照时间滚动文件夹
a1.sinks.k1.hdfs.round = true
#多少时间单位创建一个新的文件夹
a1.sinks.k1.hdfs.roundValue = 10
#重新定义时间单位
a1.sinks.k1.hdfs.roundUnit = minute
​
#多久生成一个新的文件  0代表禁用
a1.sinks.k1.hdfs.rollInterval = 0
#设置每个文件的滚动大小  1048576 = 1M
a1.sinks.k1.hdfs.rollSize = 1048576
#文件的滚动与 Event 数量无关
a1.sinks.k1.hdfs.rollCount = 0
​
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```

> 对于所有与时间相关的转义序列，Event Header中必须存在以 “timestamp”的key（除非hdfs.useLocalTimeStamp设置为true，此方法会使用TimestampInterceptor自动添加timestamp）。a1.sinks.k1.hdfs.useLocalTimeStamp = true

1. 启动监控文件夹命令

```
[root@flume0 apache-flume-1.9.0-bin]# bin/flume-ng agent --conf conf --name a1 --conf-file job/spooldir-memory-hdfs.conf -Dflume.root.logger=INFO,console
```

说明： 在使用Spooling Directory Source时

​	1)  不要在监控目录中创建并持续修改文件

​	2)  上传完成的文件会以.COMPLETED结尾

​	3)   被监控文件夹每500毫秒扫描一次文件变动

1. 向upload文件夹中添加文件

```

[root@flume0 opt]# mkdir /opt/upload
[root@flume0 upload]# touch 1.txt
[root@flume0 upload]# ls
1.txt.done
```

1. 访问hdfs 

http://flume0:50070

![](images/WEBRESOURCE681fada42da6f7f8e2a55936f382a6f2.octet-stream)

### 2.Taildir Source多目录断点续传 [重要]

​	TAILDIR类型的source在读取文件完成后，会持续读取此文件，查看此文件是否有最新的文件内容，如果有最新的文件内容会对此文件的新的内容进行读取

> 备注：flume 1.7.0推出了taildirSource组件

1) 需求：监控多个目录实现断点续传

2) 需求分析:

![](images/WEBRESOURCE9323d2597b1cabe904b0df37deada7d0.octet-stream)

3) 实现步骤

1. 在flume的job目录下创建一个conf文件

```
[root@flume0 job]# touch taildir-file-logger.conf
```

1. 在taildir-file-logger.conf文件中编写一个agent

```
a1.sources = r1
a1.channels = c1
a1.sinks = k1
​
a1.sources.r1.type = taildir
a1.sources.r1.filegroups = f1 f2
a1.sources.r1.filegroups.f1 = /opt/datas/flume/ceshi.log
a1.sources.r1.filegroups.f2 = /opt/logs/.*log
​
a1.channels.c1.type = file
​
# logger sink将采集的数据输出到控制台(日志文件中)，主要用于测试
a1.sinks.k1.type = logger
​
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```

1. 创建指定的目录和对应的文件

```
在/opt/datas/flume/目录下创建 ceshi.log文件
在/opt目录下创建logs目录
```

1. 启动agent

```
bin/flume-ng agent --conf conf  -name a1 --conf-file job/taildir-file-logger.conf -Dflume.root.logger=INFO,console
```

1. 向测试.log文件中追加内容

```
echo hello world >> ceshi.log
```

### 3.单数据源多出口案例(选择器)

单Source多Channel、Sink 如图所示:  

![](images/WEBRESOURCE3146723b6927f1a6844db845a8ce80c7.octet-stream)



1）案例需求：使用Flume-1监控文件变动，Flume-1将变动内容传递给Flume-2，

​        Flume-2负责存储到HDFS。同时Flume-1将变动内容传递给Flume-3，

​        Flume-3负责输出到Local FileSystem。

2）需求分析：

![](images/WEBRESOURCE7b7d13eee02b596cb7d0969d2d2d8ef4.octet-stream)

3）实现步骤：

1. 准备工作

```
在/opt/flume/job目录下创建group1文件夹
[root@flume0 job]# mkdir group1
​
在/opt目录下创建 datas/flume文件夹
[root@flume0 job]# mkdir -p /opt/datas/flume
```

1. 在group1目录下,创建exec-memory-avro.conf

```

[root@flume0 job]# cd group1
[root@flume0 group1]# touch exec-memory-avro.conf
​
​
# 添加内容如下
a1.sources = r1
a1.sinks = k1 k2
a1.channels = c1 c2
​
# 将数据流复制给所有channel(默认)
a1.sources.r1.selector.type = replicating
​
# tail命令查看输出的日志就是exec采集的内容
a1.sources.r1.type = exec
a1.sources.r1.command = tail -F /opt/a.log
# tail -F a.log  查看文件的最后10行，并且监听该文件产生的新的日志
​
a1.sinks.k1.type = avro
a1.sinks.k1.hostname = flume0 
a1.sinks.k1.port = 4141
​
a1.sinks.k2.type = avro
a1.sinks.k2.hostname = flume0
a1.sinks.k2.port = 4142
​
a1.channels.c1.type = memory
a1.channels.c2.type = memory
​
a1.sources.r1.channels = c1 c2
a1.sinks.k1.channel = c1
a1.sinks.k2.channel = c2
```

注：Avro是由Hadoop创始人Doug Cutting创建的一种语言无关的数据序列化和RPC框架。

注：RPC（Remote Procedure Call）—远程过程调用，它是一种通过网络从远程计算机程序上请求服 务，而不需要了解底层网络技术的协议。

1. 在group1目录下,创建avro-memory-hdfs.conf

```

[root@flume0 group1]# touch avro-memory-hdfs.conf
​
# 添加如下内容
a2.sources = r1
a2.sinks = k1
a2.channels = c1
​
a2.sources.r1.type = avro
a2.sources.r1.bind = flume0
a2.sources.r1.port = 4141
​
a2.channels.c1.type = memory
​
a2.sinks.k1.type = hdfs
a2.sinks.k1.hdfs.path = hdfs://flume0:9000/flume2/%Y-%m-%d
a2.sinks.k1.hdfs.useLocalTimeStamp = true
​
a2.sources.r1.channels = c1
a2.sinks.k1.channel = c1
```

1. 在group1目录下,创建avro-memory-file.conf

```
[root@flume0 group1]# touch avro-memory-file.conf
​
# 添加如下内容
a3.sources = r1
a3.sinks = k1
a3.channels = c2
​
a3.sources.r1.type = avro
a3.sources.r1.bind = flume0
a3.sources.r1.port = 4142
​
a3.channels.c2.type = memory
​
a3.sinks.k1.type = file_roll
a3.sinks.k1.sink.directory = /opt/datas/flume
a3.sinks.k1.sink.rollInterval = 0
​
a3.sources.r1.channels = c2
a3.sinks.k1.channel = c2
```

提示：输出的本地目录必须是已经存在的目录，如果该目录不存在，并不会创建新的目录。

1. 执行配置文件

```
[root@flume0 apache-flume-1.9.0-bin]# bin/flume-ng agent --conf conf --name a3 --conf-file job/group1/avro-memory-file.conf
​
[root@flume0 apache-flume-1.9.0-bin]# bin/flume-ng agent --conf conf --name a2 --conf-file job/group1/avro-memory-hdfs.conf
​
[root@flume0 apache-flume-1.9.0-bin]# bin/flume-ng agent --conf conf --name a1 --conf-file job/group1/exec-memory-avro.conf
```

1. 提交测试数据

```

[root@flume0 opt]# echo hahaha >> a.log
```

1. 检查HDFS上数据

![](images/WEBRESOURCE2d65804b5970f02c483ec465e7a8be30.octet-stream)

### 4.多数据源汇总案例

多Source汇总数据到单Flume

![](images/WEBRESOURCE5d4bba9f718f7eee7d017f6916cdf7dd.octet-stream)

1）案例需求：

​      实现将多个agent的数据发送给一个agent

2）需求分析：

![](images/WEBRESOURCE13b954c72c3269a837139a4e7f24e494.octet-stream)

3）实现步骤：

1. 准备工作

```
在flume/job目录下创建group2文件夹
[root@flume0 job]# mkdir group2
```

1. 在group2目录下,创建demo6-agent1.conf

```
[root@flume0 group2]# touch agent1.conf
​
# 内容如下
a1.sources = r1
a1.channels = c1
a1.sinks = k1
​
a1.sources.r1.type = exec
a1.sources.r1.command = tail -F /opt/app/a.log
​
a1.channels.c1.type = memory
​
a1.sinks.k1.type = avro
a1.sinks.k1.hostname = flume0
a1.sinks.k1.port = 6661
​
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```

1. 在group2目录下,创建agent2.conf

```
[root@flume0 group2]# touch agent2.conf
​
# 内容如下
a2.sources = r1
a2.channels = c1
a2.sinks = k1
​
a2.sources.r1.type = exec
a2.sources.r1.command = tail -F /opt/app/b.log
​
a2.channels.c1.type = memory
​
a2.sinks.k1.type = avro
a2.sinks.k1.hostname = flume0
a2.sinks.k1.port = 6661
​
a2.sources.r1.channels = c1
a2.sinks.k1.channel = c1
```

1. 在group2目录下,创建agent3.conf

```
[root@flume0 group2]# touch agent3.conf
​
# 内容如下
a3.sources = r1
a3.channels = c1
a3.sinks = k1
​
a3.sources.r1.type = avro
a3.sources.r1.bind = flume0
a3.sources.r1.port = 6661
​
a3.channels.c1.type = memory
​
a3.sinks.k1.type = logger
​
a3.sources.r1.channels = c1
a3.sinks.k1.channel = c1
```

1. 执行配置文件

```
[root@flume0 apache-flume-1.9.0-bin]# bin/flume-ng agent --conf conf --name a3 --conf-file job/group2/agent3.conf -Dflume.root.logger=INFO,console
​
[root@flume0 apache-flume-1.9.0-bin]# bin/flume-ng agent --conf conf --name a2 --conf-file job/group2/agent2.conf
​
[root@flume0 apache-flume-1.9.0-bin]# bin/flume-ng agent --conf conf --name a1 --conf-file job/group2/agent1.conf
```

1. 使用echo命令向a.log和b.log追加内容

### 5. 实时监控文件目录下的多个新文件

使用 flume文档中 的Spooling Directory Source，具体配置如下

```
# Name部分，命名组件名称 a1是当前agent的名字 
a1.sources = r1
a1.sinks = k1
a1.channels = c1

#监控路径的 Spooling Directory 模型
a1.sources.r1.type = spooldir
a1.sources.r1.channels = ch-1
a1.sources.r1.spoolDir = /flume/test
a1.sources.r1.fileHeader = true
#这里可以使用ignorePattern 配合正则忽略某种类型的文件


# 这是channel的类型，容量，事务容量
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100


# 这是 sink的类型
a1.sinks.k1.type = hdfs
# 保存的HDFS路径/后的为按照时间划分
a1.sinks.k1.hdfs.path = hdfs://hadoop104:9000/flume/%Y-%m-%d/%H
# 文件前缀
a1.sinks.k1.hdfs.filePrefix = logs-
# 是否按照时间滚动文件夹
a1.sinks.k1.hdfs.round = true
#多少时间单位创建一个新的文件夹,结合roundUnit使用
a1.sinks.k1.hdfs.roundValue = 10
#重新定义时间单位
a1.sinks.k1.hdfs.roundUnit = minute
# 是否使用本地时间戳,因为根据时间生成目录的,必须搞成true，不开启读不到时间
a1.sinks.k1.hdfs.useLocalTimeStamp = true
# 攒够多少event上传Hdfs一次
a1.sinks.k1.hdfs.batchSize = 100
#设置文件类型，可以支持压缩
a1.sinks.k1.hdfs.fileType = DataStream
# 多久生成一个新的文件
a1.sinks.k1.hdfs.useLocalTimeStamp = true
#多久生成一个新的文件  0代表禁用
a1.sinks.k1.hdfs.rollInterval = 30
#设置每个文件的滚动大小  1048576 = 1M
a1.sinks.k1.hdfs.rollSize = 1048576
#文件的滚动与 Event 数量无关
a1.sinks.k1.hdfs.rollCount = 0


#绑定
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1

```

该数据源会将处理过的文件打上.completed的后缀 该后缀支持自定义（fileSuffix）

测试结果如下，注意，不要尝试丢同名文件，会挂

![](images/WEBRESOURCE36027a5d6df92dabf2efddde4310bfd3.octet-stream)



### 6. 实时监控目录下的多个追加文件

由于Spooling Directory 模型尝试丢同名文件，会挂

官方在1.7之后更新了Taildir Source模型，配置文件如下

```
# Name部分，命名组件名称 a1是当前agent的名字 
a1.sources = r1
a1.sinks = k1
a1.channels = c1

#监控路径的 Spooling Directory 模型
a1.sources.r1.type = taildir
# 文件组
a1.sources.r1.filegroups = filegroups1
# 文件组目录
a1.sources.r1.filegroups.filegroups1 = /flume/test/.*.*
# 记录文件
a1.sources.r1.positionFile = /flume/test/tail_dir.json


# 这是channel的类型，容量，事务容量
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100


# 这是 sink的类型
a1.sinks.k1.type = hdfs
# 保存的HDFS路径/后的为按照时间划分
a1.sinks.k1.hdfs.path = hdfs://hadoop104:9000/flume/%Y-%m-%d/%H
# 文件前缀
a1.sinks.k1.hdfs.filePrefix = logs-
# 是否按照时间滚动文件夹
a1.sinks.k1.hdfs.round = true
#多少时间单位创建一个新的文件夹,结合roundUnit使用
a1.sinks.k1.hdfs.roundValue = 10
#重新定义时间单位
a1.sinks.k1.hdfs.roundUnit = minute
# 是否使用本地时间戳,因为根据时间生成目录的,必须搞成true，不开启读不到时间
a1.sinks.k1.hdfs.useLocalTimeStamp = true
# 攒够多少event上传Hdfs一次
a1.sinks.k1.hdfs.batchSize = 100
#设置文件类型，可以支持压缩
a1.sinks.k1.hdfs.fileType = DataStream
# 多久生成一个新的文件
a1.sinks.k1.hdfs.useLocalTimeStamp = true
#多久生成一个新的文件  0代表禁用
a1.sinks.k1.hdfs.rollInterval = 30
#设置每个文件的滚动大小  1048576 = 1M
a1.sinks.k1.hdfs.rollSize = 1048576
#文件的滚动与 Event 数量无关
a1.sinks.k1.hdfs.rollCount = 0


#绑定
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```

文件的存储记录 配置在positionFile中，记录内容如下

```
[
    {
        "inode": 35491201,
        "pos": 203,
        "file": "/flume/test/ceshi.log.COMPLETED"
    },
    {
        "inode": 35987356,
        "pos": 2,
        "file": "/flume/test/aaa.txt.COMPLETED"
    },
    {
        "inode": 35987357,
        "pos": 17,
        "file": "/flume/test/2.txt.COMPLETED"
    },
    {
        "inode": 35989191,
        "pos": 0,
        "file": "/flume/test/tail_dir.json"
    }
]
```

可以看到，追加后的文件pos会更改记录，json中的内容，indoe指linux文件的唯一标识；pos指操作过的字节数，file为绝对路径

此处我们会涉及到一个问题，当文件重命名时，inode虽然没有变，但是记录过的inode值的路径并不是原来的绝对路径了，那么flume会重新上传一份这个文件的内容至hadoop，如果我们写死处理某个单个文件，也会存在flume挂掉丢失数据的可能，那么此时最好的解决方案就是重写部分源码

### 7.重写源码解决实时监控可能存在的问题

路径1：org.apache.flume.source.taildir.TailFile.updatePos

路径2：org.apache.flume.source.taildir.ReliableTaildirEventReader.updateTailFiles

如下图所示，改完打包就行了

![](images/WEBRESOURCE37559b8105cd4f7008fce3507fefe073.octet-stream)

## 四、Flume 进阶

#### 1.关于Flume事务问题

我们先来看结构图

![](images/WEBRESOURCE0764c369149f17a1cfd6db656b99078d.octet-stream)

对于推送事件来说，每份数据都是先通过doPut写入缓冲区putList，然后通过doCommit检查队列是否足够合并，如果空间不足则直接doRollback回滚

对于Sink拉取事件来说，doTask先将数据读至takeList缓冲，并将数据发送。如果全部发送，则清空缓冲区，如果发送异常，则rollback临时缓冲区，将缓冲的数据返还给队列

#### 2.Flume Agent内部原理

我们来看下agent的内部原理图

![](images/WEBRESOURCE9a71a5d4fe390510a01e550ac4a9a656.octet-stream)



我们将这个架构图拆分为三部分来看

Channel处理信道会经过InterCeptor的过滤器链，这部分我们可以自定义去处理脏数据等数据问题

Channel Selector 信道选择器，我们可以采用两种，轮询和广播模式

SinkProcessor的三种模式，其中LoadBalancingSinkPorcessor 为轮询策略；FailoverSinkProcessor为异常策略，简单来说是指调整sink权重，如果权重级别较高的sink挂掉，则交予权重次级的sink处理

#### 3. Flume拓扑结构

 3.1 简单串联结构

![](images/WEBRESOURCE45da08ea90c2a71e109c8dfd33a34359.octet-stream)

这种模式是将多个 flume 顺序连接起来了，从最初的 source 开始到最终 sink 传送的目的存储系统。此模式不建议桥接过多的 flume 数量，flume 数量过多不仅会影响传输速率，而且一旦传输过程中某个节点 flume 宕机，会影响整个传输系统。

 3.2 复制和多路复用

![](images/WEBRESOURCEa153a627e3cf494d33438a555f74a70e.octet-stream)

Flume 支持将事件流向一个或者多个目的地。这种模式可以将相同数据复制到多个channel 中，或者将不同数据分发到不同的 channel 中，sink 可以选择传送到不同的目的地

 3.3 负载均衡与故障转移

![](images/WEBRESOURCE3924f12b1d235717c90f87fc552e1d70.octet-stream)

flume 支持使用多个Sink ，在逻辑上分为一个sink组，sink组通过不同的SinkProcessor来实现负载均衡和故障转移

 3.4 聚合

![](images/WEBRESOURCE144b26585644249e24d72b10fb683942.octet-stream)

flume大数据集群方案，这种模式是最常见的大数据方案，日常web应用分布在上百个服务器，处理起来非常麻烦，使用这种组合方式



#### 4. 企业开发案例

flume - maven

```
    <dependencies>
        <dependency>
            <groupId>org.apache.flume</groupId>
            <artifactId>flume-ng-core</artifactId>
            <version>1.9.0</version>
        </dependency>
    </dependencies>
```

 4.1 单数据源多出口案例

1）案例需求：

使用Flume-1监控文件变动，Flume-1将变动的内容传给Flume-2，Flume-2负责存储HDFS,同时Flume-1将数据传给Flume-3，Flume-3负责输出Local

2）架构设计：

![](images/WEBRESOURCE110a18579f20f0b33f2a52405c10c4aa.octet-stream)

3）实现：

 flume-file-flume.conf

```
#name
a1.sources = r1
a1.sinks = k1 k2
a1.channels = c1 c2

# 数据源来自文件
#监控路径的 Spooling Directory 模型
a1.sources.r1.type = taildir
# 文件组
a1.sources.r1.filegroups = filegroups1
# 文件组目录
a1.sources.r1.filegroups.filegroups1 = /flume/test/.*.*
# 记录文件
a1.sources.r1.positionFile = /flume/test/tail_dir.json

# 将数据流赋值给所有channel
a1.sources.r1.selector.type = replicating

#信道
# 这是channel的类型，容量，事务容量
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

a1.channels.c2.type = memory
a1.channels.c2.capacity = 1000
a1.channels.c2.transactionCapacity = 100

#算子，两个算子都是avro发送者，我们本机测试 就发本地把
a1.sinks.k1.type = avro
a1.sinks.k1.hostname = hadoop104
a1.sinks.k1.port = 9999

a1.sinks.k2.type = avro
a1.sinks.k2.hostname = hadoop104
a1.sinks.k2.port = 9998

#组合
a1.sources.r1.channels = c1 c2
a1.sinks.k1.channel = c1
a1.sinks.k2.channel = c2
```

flume-flume-hdfs.conf

```
#name
a3.sources = r4
a3.sinks = k4
a3.channels = c4

# 数据源来自flume avro
a3.sources.r4.type = avro
# 要监听的主机
a3.sources.r4.bind = hadoop104
a3.sources.r4.port = 9998

#信道
# 这是channel的类型，容量，事务容量
a3.channels.c4.type = memory
a3.channels.c4.capacity = 1000
a3.channels.c4.transactionCapacity = 100

# 这是 sink的类型
a3.sinks.k4.type = hdfs
# 保存的HDFS路径/后的为按照时间划分
a3.sinks.k4.hdfs.path = hdfs://hadoop104:9000/flume/%Y-%m-%d/%H
# 文件前缀
a3.sinks.k4.hdfs.filePrefix = logs-
# 是否按照时间滚动文件夹
a3.sinks.k4.hdfs.round = true
#多少时间单位创建一个新的文件夹,结合roundUnit使用
a3.sinks.k4.hdfs.roundValue = 10
#重新定义时间单位
a3.sinks.k4.hdfs.roundUnit = minute
# 是否使用本地时间戳,因为根据时间生成目录的,必须搞成true，不开启读不到时间
a3.sinks.k4.hdfs.useLocalTimeStamp = true
# 攒够多少event上传Hdfs一次
a3.sinks.k4.hdfs.batchSize = 100
#设置文件类型，可以支持压缩
a3.sinks.k4.hdfs.fileType = DataStream
# 多久生成一个新的文件
a3.sinks.k4.hdfs.useLocalTimeStamp = true
#多久生成一个新的文件  0代表禁用
a3.sinks.k4.hdfs.rollInterval = 30
#设置每个文件的滚动大小  1048576 = 1M
a3.sinks.k4.hdfs.rollSize = 1048576
#文件的滚动与 Event 数量无关
a3.sinks.k4.hdfs.rollCount = 0


#组合
a3.sources.r4.channels = c4
a3.sinks.k4.channel = c4
```

flume-flume-flie.conf

```
#name
a2.sources = r3
a2.sinks = k3
a2.channels = c3

# 数据源来自flume avro
a2.sources.r3.type = avro
# 要监听的主机
a2.sources.r3.bind = hadoop104
a2.sources.r3.port = 9999

#信道
# 这是channel的类型，容量，事务容量
a2.channels.c3.type = memory
a2.channels.c3.capacity = 1000
a2.channels.c3.transactionCapacity = 100

#输出文件目录
a2.sinks.k3.type = file_roll
a2.sinks.k3.sink.directory = /flume/flume/jobs/group1/test/

#组合
a2.sources.r3.channels = c3
a2.sinks.k3.channel = c3
```

效果如下：

Dir:

![](images/WEBRESOURCE36a251888121e05f00ed81e8998201fc.octet-stream)

HDFS：

![](images/WEBRESOURCEcabd7b6ef4008aa6d6147e8dec15f729.octet-stream)



 4.2 负载均衡和故障转移

1）案例需求

使用Flume监控一个端口，其中Sink组分别对接flume2，flume3，采用FailoverSinkProcessor，实现故障转移的功能。



2）案例架构

![](images/WEBRESOURCEd430ea4e7acb0a9936a990de1135026d.octet-stream)



3）落地配置实现

优先配置flume-1

```
# name
a1.sources = r1
a1.channels = c1
#设置算子组
a1.sinkgroups = g1
a1.sinks = k1 k2

# 数据源 来自netcat监听
# Describe/configure the source
a1.sources.r1.type = netcat
a1.sources.r1.bind = localhost
a1.sources.r1.port = 44444

# 信道
# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100


#将算子绑定算子组
a1.sinkgroups.g1.sinks = k1 k2
#设置故障转移
a1.sinkgroups.g1.processor.type = failover
#绑定算子权重
a1.sinkgroups.g1.processor.priority.k1 = 5
a1.sinkgroups.g1.processor.priority.k2 = 10
a1.sinkgroups.g1.processor.maxpenalty = 10000

#设置单个算子
#算子，两个算子都是avro发送者，我们本机测试 就发本地把
a1.sinks.k1.type = avro
a1.sinks.k1.hostname = hadoop104
a1.sinks.k1.port = 9999

a1.sinks.k2.type = avro
a1.sinks.k2.hostname = hadoop104
a1.sinks.k2.port = 9998


#绑定
a1.sources.r1.channels = c1
a1.sinkgroups.g1.sinks = k1 k2
a1.sinks.k1.channel = c1
a1.sinks.k2.channel = c1
```

然后编辑接收端 这个复制两个改改就行了

```
#name
a2.sources = r1
a2.sinks = k1
a2.channels = c1

# 数据源来自flume avro
a2.sources.r1.type = avro
# 要监听的主机
a2.sources.r1.bind = hadoop104
a2.sources.r1.port = 9998

#打印到控制台
a2.sinks.k1.type = logger


# 信道
# Use a channel which buffers events in memory
a2.channels.c1.type = memory
a2.channels.c1.capacity = 1000
a2.channels.c1.transactionCapacity = 100

#绑定
a2.sources.r1.channels = c1
a2.sinks.k1.channel = c1
```

验证的时候 停一台就可以看见



 4.3 多合一输出

1）案例需求：

Flume-1 ，Flume-2 监控某一个端口的数据流，Flume-1 与 Flume-2 将数据发送给 Flume-3，Flume-3 将最终数据打印到控制台。

2）需求分析：

![](images/WEBRESOURCEdb69c3986b8bc14e599c75366878f7cf.octet-stream)

3）实现

装配数据源X2

```
#name 
a1.sources = r1
a1.channels = c1
a1.sinks = k1

# 监听44444
a1.sources.r1.type = netcat
a1.sources.r1.bind = localhost
a1.sources.r1.port = 44444

# 信道
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

#算子
a1.sinks.k1.type = avro
a1.sinks.k1.hostname = hadoop104
a1.sinks.k1.port = 9999

# 绑定
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```

装配接受客户端

```
#name 
a3.sources = r1
a3.channels = c1
a3.sinks = k1

# 数据源来自flume avro
a3.sources.r1.type = avro
# 要监听的主机
a3.sources.r1.bind = hadoop104
a3.sources.r1.port = 9999

# Describe the sink
a3.sinks.k1.type = logger

# Use a channel which buffers events in memory
a3.channels.c1.type = memory
a3.channels.c1.capacity = 1000
a3.channels.c1.transactionCapacity = 100

# 绑定
a3.sources.r1.channels = c1
a3.sinks.k1.channel = c1
```

 4.4 自定义拦截器 Interceptor

1）案例需求：

使用 Flume 采集服务器本地日志，需要按照日志类型的不同，将不同种类的日志发往不同的分析系统

2）需求分析：

在实际的开发中，一台服务器产生的日志类型可能有很多种，不同类型的日志可能需要发送到不同的分析系统。

此时会用到 Flume 拓扑结构中的 Multiplexing 结构，Multiplexing的原理是，根据 event 中 Header 的某个 key 的值，将不同的 event 发送到不同的 Channel中，所以我们需要自定义一个 Interceptor，为不同类型的 event 的 Header 中的 key 赋予不同的值。在该案例中，我们以端口数据模拟日志，以是否包含”atguigu”模拟不同类型的日志，我们需要自定义 interceptor 区分数据中是否包含”关键字”，将其分别发往不同的分析系统（Channel）。

3) 编码

```
package com.wyt.fliter;

import com.google.common.collect.Lists;
import org.apache.flume.Context;
import org.apache.flume.Event;
import org.apache.flume.interceptor.Interceptor;

import java.util.List;
import java.util.Map;

/***
 * 自定义拦截器
 */
public class TypeInterceptor implements Interceptor {
    //header中的key
    private static final String key = "type";
    //判断value是否包含该关键字
    private static final String dataValue = "wyt";
    //存放处理后的事件
    private static List<Event> eventList = Lists.newArrayList();
    /**
     * 初始化
     */
    @Override
    public void initialize() {

    }

    /**
     * 单个事件处理方法 当body中包含wyt时，将type设置为wyt，否则设置为other
     * @param event
     * @return
     */
    @Override
    public Event intercept(Event event) {
        //头信息
        Map<String, String> headers = event.getHeaders();
        //获取body
        String body = new String(event.getBody());
        //解析信息
        if(body.contains(dataValue)){
            headers.put(key,"wyt");
        }else{
            headers.put(key,"other");
        }
        //返回数据
        return event;
    }

    /**
     * 批量事件处理方法
     * @param list
     * @return
     */
    @Override
    public List<Event> intercept(List<Event> list) {
        //清空集合
        eventList.clear();
        //遍历
        for (Event event:list) {
            eventList.add(intercept(event));
        }
        return eventList;
    }

    /**
     * 关闭资源
     */
    @Override
    public void close() {

    }

    /**
     * 建造者模式
     */
    public static class Builder implements Interceptor.Builder{

        @Override
        public Interceptor build() {
            return new TypeInterceptor();
        }

        @Override
        public void configure(Context context) {

        }
    }
}

```

4）配置

```
#name
a1.sources = r1
a1.sinks = k1 k2
a1.channels = c1 c2

# 监听44444
a1.sources.r1.type = netcat
a1.sources.r1.bind = localhost
a1.sources.r1.port = 44444

#设置拦截器 i1
a1.sources.r1.interceptors = i1
a1.sources.r1.interceptors.i1.type = com.wyt.fliter.TypeInterceptor$Builder
a1.sources.r1.selector.type = multiplexing
#这里是监听的头部关键字
a1.sources.r1.selector.header = type
#头部关键字type的value是wyt 去C1,是other 去C2
a1.sources.r1.selector.mapping.wyt = c1
a1.sources.r1.selector.mapping.other = c2

# Describe the sink
a1.sinks.k1.type = avro
a1.sinks.k1.hostname = localhost
a1.sinks.k1.port = 4141

a1.sinks.k2.type=avro
a1.sinks.k2.hostname = localhost
a1.sinks.k2.port = 4242

# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100
# Use a channel which buffers events in memory
a1.channels.c2.type = memory
a1.channels.c2.capacity = 1000
a1.channels.c2.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1 c2
a1.sinks.k1.channel = c1
a1.sinks.k2.channel = c2
```

监听flume同4.1的控制台输出即可。



 4.5 自定义数据源 Source

1）简介：

Source 是负责接收数据到 Flume Agent 的组件。Source 组件可以处理各种类型、各种格式的日志数据，包括 avro、thrift、exec、jms、spooling directory、netcat、sequencegenerator、syslog、http、legacy。官方提供的 source 类型已经很多，但是有时候并不能满足实际开发当中的需求，此时我们就需要根据实际需求自定义某些 source。

官方也提供了自定义 source 的接口：https://flume.apache.org/FlumeDeveloperGuide.html#source 

根据官方说明自定义MySource 需要继承 AbstractSource 类并实现 Configurable 和 PollableSource 接口。

实现相应方法：

```
//backoff 步长
getBackOffSleepIncrement()
//backoff 最长时间
getMaxBackOffSleepInterval()
//初始化 context（读取配置文件内容）
configure(Context context)
//获取数据封装成 event 并写入 channel 这个方法将被循环调用
process()
```

使用场景：读取 MySQL 数据或者其他文件系统。

2）需求：

使用 flume 接收数据，并给每条数据添加前缀，输出到控制台。前缀可从 flume 配置文件中配置

![](images/WEBRESOURCE68d26940f55743fcc3dfa4070d89db1b.octet-stream)

3）分析图：

![](images/WEBRESOURCEd92461505a0c768ba187df71068f6153.octet-stream)

4）编码

我们使用process的假数据源模拟数据

```
package com.wyt.source;

import org.apache.flume.Context;
import org.apache.flume.EventDeliveryException;
import org.apache.flume.PollableSource;
import org.apache.flume.conf.Configurable;
import org.apache.flume.event.SimpleEvent;
import org.apache.flume.source.AbstractSource;

import java.util.HashMap;
import java.util.Map;

/**
 * flume - 自定义数据源
 */
public class MySource extends AbstractSource implements Configurable, PollableSource {

    //定义配置文件将来要读取的字段
    private Long delay;
    private String field;
    private Map<String, String> headers;

    /**
     * 初始化信息
     * @param context
     */
    @Override
    public void configure(Context context) {
        delay = context.getLong("delay");
        field = context.getString("field", "Hello!");
        //创建请求头
        headers = new HashMap<>();
        headers.put("type", "wyt");
    }


    @Override
    public Status process() throws EventDeliveryException {
        try{

            //创建事件
            SimpleEvent event = new SimpleEvent();
            //创建一个假的数据源
            for (int i = 0; i < 5; i++) {
                //请求头
                event.setHeaders(headers);
                //请求体
                event.setBody((field + i).getBytes());
                //将事件写入 channel
                getChannelProcessor().processEvent(event);
                Thread.sleep(delay);
            }
        }catch (Exception e){
            e.printStackTrace();
            return Status.BACKOFF;
        }
        return Status.READY;
    }

    @Override
    public long getBackOffSleepIncrement() {
        return 0;
    }

    @Override
    public long getMaxBackOffSleepInterval() {
        return 0;
    }
}
```

5）配置文件：

```
# Name the components on this agent
a1.sources = r1
a1.sinks = k1
a1.channels = c1
# Describe/configure the source
a1.sources.r1.type = com.wyt.source.MySource
a1.sources.r1.delay = 1000
#a1.sources.r1.field = atguigu
# Describe the sink
a1.sinks.k1.type = logger
# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100
# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```

![](images/WEBRESOURCEcb447027a7db81bf6932010587acfb85.octet-stream)

 4.6 自定义 Sink

1）介绍：

Sink 不断地轮询 Channel 中的事件且批量地移除它们，并将这些事件批量写入到存储或索引系统、或者被发送到另一个 Flume Agent。Sink 是完全事务性的。在从 Channel 批量删除数据之前，每个 Sink 用 Channel 启动一个事务。批量事件一旦成功写出到存储系统或下一个 Flume Agent，Sink 就利用 Channel 提交事务。事务一旦被提交，该 Channel 从自己的内部缓冲区删除事件。Sink 组件目的地包括 hdfs、logger、avro、thrift、ipc、file、null、HBase、solr、自定义。官方提供的 Sink 类型已经很多，但是有时候并不能满足实际开发当中的需求，此时我们就需要根据实际需求自定义某些 Sink。

官方也提供了自定义 sink 的接口：https://flume.apache.org/FlumeDeveloperGuide.html#sink 

根据官方说明自定义MySink 需要继承 AbstractSink 类并实现 Configurable 接口。

实现相应方法：

```
//初始化 context（读取配置文件内容）
configure(Context context)
//从 Channel 读取获取数据（event），这个方法将被循环调用。使用场景：读取 Channel 数据写入 MySQL 或者其他文件系统。
process()
```

2）需求 ：

使用 flume 接收数据，并在 Sink 端给每条数据添加前缀和后缀，输出到控制台。前后缀可在 flume 任务配置文件中配置

流程分析：

![](images/WEBRESOURCEabb12f3d6781a1f1e85e63543ac6e482.octet-stream)

3) 编码：

```
package com.wyt.sink;

import org.apache.flume.*;
import org.apache.flume.conf.Configurable;
import org.apache.flume.sink.AbstractSink;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.util.Objects;

/**
 * flume 自定义输出算子
 * 由于 Source 的事务是通过信道制作的，
 * 而Sink的事务是通过自己的事务管理器来实现的，
 * 如果要实现事务，需要重写start和stop方法
 * 如果sink的事务处理结束，那么会返回信道告知消息未丢失，从而删除信道中的数据
 */
public class MySink extends AbstractSink implements Configurable {

    //定义前缀 后缀
    private String prefix;
    private String suffix;
    //创建Log对象
    private Logger logger  = LoggerFactory.getLogger(MySink.class);

    /**
     * 重写start
     */
    @Override
    public synchronized void start() {
        super.start();
    }


    /**
     * 读取配置文件
     *
     * @param context
     */
    @Override
    public void configure(Context context) {
        //读取配置文件的前后缀
        prefix = context.getString("prefix", "我是默认前缀--");
        suffix = context.getString("suffix", "--我是默认后缀");
    }

    /**
     * 拉取信息
     *
     * @return
     * @throws EventDeliveryException
     */
    @Override
    public Status process() throws EventDeliveryException {
        //1.获取信道,获取事务,开启事务
        Channel channel = getChannel();
        Transaction transaction = channel.getTransaction();
        transaction.begin();

        try {
            //2.从信道中获取数据
            Event event = null;
            while (true) {
                event = channel.take();
                if (Objects.nonNull(event)) {
                    //数据可能为空，抓取不到就一直抓！
                    break;
                }
            }
            //3.处理事件，此处只是打印到控制台,顺便拼接前后缀,此处为控制台打印，所以
            //执行要添加  -Dflume.root.logger=INFO,console
            logger.info(prefix + new String(event.getBody()) + suffix);
            //4.提交事务
            transaction.commit();
            //5.返回状态
            return Status.READY;
        } catch (Exception e) {
            //出现异常,回滚数据
            transaction.rollback();
            return Status.BACKOFF;
        } finally {
            transaction.close();
        }
    }

    /**
     * 重写stop
     */
    @Override
    public synchronized void stop() {
        super.stop();
    }

}

```

编写配置 测试即可，此处省略配置了。