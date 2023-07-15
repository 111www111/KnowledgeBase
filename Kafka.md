                            

# 什么是消息中间件

## 概念

消息中间件是基于队列与消息传递技术，在网络环境中为应用系统提供同步或异步、可靠的消息传输的支撑性软件系统。

## 队列的数据结构

队列是一种线性表。它允许在表的一端插入数据，在另一端删除元素。插入元素的这一端称之为队尾。删除元素的这一端我们称之为队首。

### 队列的特性

1. 在队尾插入元素，在队首删除元素

1. FIFO（先进先出），就向排队取票一样

### 队列的分类

1. 顺序队列

1. 循环队列

![](images/WEBRESOURCE294be8cd84ef90de5674561a32ecd0a5.octet-stream)

![](images/WEBRESOURCE2a3e14cf72abe415f5a35bdb53e1b4b8.octet-stream)

## 什么是消息

消息就是应用程序中间传输的数据，例如：用户下的订单、用户发表的评论、网站用户行为跟踪、各种传感器采集的数据等等，这些都是消息，所以消息就是数据的等价名词。

## 消息中间件的应用

消息中间件利用高效可靠的消息传递机制，进行平台无关的数据交流，并基于数据通信来进行分布式系统的集成。通过提供消息传递和消息排队模型，它可以在分布式环境下扩展进程间的通信。

## 消息队列

大部分消息中间件，都是通过消息队列这种技术来实现的，例如：ActiveMQ、RabbitMQ、ZeroMQ、Kafka、MetaMQ、RocketMQ等。

![](images/WEBRESOURCEa8919af26889d31c4552dad8827863d1.octet-stream)

# 消息中间件的应用场景

## 1.异步处理

应用场景，例如用户注册完账号后，需要邮件和短信通知用户。传统的方式是同步处理的，使用中间件就可以实现异步处理，提供系统处理效率。

## 2.流量削峰

流量削峰就是某个时间段流量特别高，需要特殊处理。例如：淘宝双十一、秒杀获得、抢购活动。这个时候流量特别高，可以把用户请求发送到消息中间件排队处理，这样既可以系统压力，又不会让消息丢失。

## 3.应用解耦

在常见的订单支付场景中，为了鼓励用户多下单，运营经常会搞各类促销活动。当用户订单支付（订单服务）成功后，就送用户XXX优惠券（优惠券服务）；给用户增加积分（积分服务）等等。

## 4.日志处理

在一个大型应用的开发中，应用程序通常部署几台，甚至几十台应用服务器上。此时，开发人员和运维人员查询日志时，需要到服务对应的服务器上，去跟踪无疑是相当的不方便。此时，需要一个集中化的日志管理系统，现在比较流行的方案就是ELK(elasticsearch、logstash、kibana) +Kakfa的方案。

## 5.分布式事物

分布式事务就是实现跨数据库的事务支持。这个比较复杂，这里就不展开来讲了。    

# 消息中间件常用协议

## JMS协议

JMS即Java消息服务（Java Message Service）应用程序接口，是一个Java平台中关于面向消息中间件（MOM）的API，用于在两个应用程序之间，或分布式系统中发送消息，进行异步通信。Java消息服务是一个与具体平台无关的API，绝大多数MOM提供商都对JMS提供支持。

## AMQP协议

AMQP（Advanced Message Queuing Protocol）,一个提供统一消息服务，应用层标准高级消息队列协议，是应用层协议的一个开放标准，为面向消息的中间件设计。基于此协议的客户端与消息中间件可传递消息，并不受客户端/中间件不同产品，不同开发语言等条件的限制。

优点：可靠、通用

官网：https://www.amqp.org/

## MQTT协议

MQTT（Message Queuing Telemetry Transport，消息队列遥测传输）是IBM开发的一个即时通讯协议，有可能成为物联网的重要组成部分。该协议支持所有平台，几乎可以把所有联网物品和外部连接起来，被用来当做传感器和致动器（比如通过Twitter让房屋联网）的通信协议。

优点：格式简洁、占用带宽小、移动端通信、PUSH、嵌入式系统

官网：https://mqtt.org/

## STOMP协议

STOMP（Streaming Text Orientated Message Protocol）是流文本定向消息协议，是一种为MOM(Message Oriented Middleware，面向消息的中间件)设计的简单文本协议。STOMP提供一个可互操作的连接格式，允许客户端与任意STOMP消息代理（Broker）进行交互。

优点：命令模式（非topic\queue模式）

官网：http://stomp.github.io/

## XMPP协议

XMPP（可扩展消息处理现场协议，Extensible Messaging and Presence Protocol）是基于可扩展标记语言（XML）的协议，多用于即时消息（IM）以及在线现场探测。适用于服务器之间的准即时操作。核心是基于XML流传输，这个协议可能最终允许因特网用户向因特网上的其他任何人发送即时消息，即使其操作系统和浏览器不同。

优点：通用公开、兼容性强、可扩展、安全性高，但XML编码格式占用带宽大

官网：https://xmpp.org/

## 其他基于TCP/IP自定义的协议

有些特殊框架（如：redis、kafka、zeroMq等）根据自身需要未严格遵循MQ规范，而是基于TCP/IP自行封装了一套协议，通过网络socket接口进行传输，实现了MQ的功能。                                

# 消息中间件的模式

消息中间件的模式是指，消息的发送和接收模式。大致分为两种：

1. 点对点模式。

1. 发布订阅模式

## 点对点模式

在点对点模型中，即使许多消息接收者在同一个消息队列中侦听，消息从消息发送者发送到只有一个接收者。在点对点模型中，通常使用术语消息发送者和消息接收者，而不是消息发布者和消息消费者。图示如下。

![](images/WEBRESOURCE08c7b78682e40f95f34cb04bd757a50f.octet-stream)

有两种类型的点对点消息传递：

1. 即发即弃（单向）

1. 消息传递和请求/回复（请求-响应）消息传递。

### 即发即弃

在即发即弃中，消息发送方不会等待来自消息队列的任何响应。它不关心消息队列是否收到消息。在这个模型中，发起者和接收者根本没有交互。

### 请求/回复消息模型

与即发即弃模式不同，在请求/回复消息模型中，消息发送方在一个队列上发送一条消息，然后等待接收方的响应。使用这种模型，发送关心它是否收到或尚未收到的消息状态。

![](images/WEBRESOURCEb39ba019488ccd487b78d678b03df109.octet-stream)

## 发布订阅模式

发布/订阅消息传递或通常称为发布/订阅消息传递是一种异步形式。在该域中，消息生产者称为发布者，消息消费者称为订阅者。发布者向某个主题生成消息，然后订阅该主题的所有订阅者都将收到发送消息并消费它们。点对点和发布/订阅消息模型之间的区别在于消息的接收者数量。另一个区别是在点对点模型中，消息发送者必须知道接收者，但在发布/订阅中，消息发布者不需要知道消息将在哪里消费。该特性在应用发布/订阅消息模型时为应用程序提供了高度解耦。

![](images/WEBRESOURCEa028b72dc233b2e064b61f857527e1f3.octet-stream)

# kafka简介

## 简介

Kafka是一个分布式的流处理平台。一个流处理平台通常有以下特征

1. 发布和订阅消息流（类似于消息队列或者企业级的消息系统）

1. 以容错的、持久的方式存储消息流

1. 当消息流到来的时候，处理消息

Kafka是一个分布式、分区的、多副本的、多订阅者，基于zookeeper协调的分布式日志系统，其主要特点为：

1. 以时间复杂度为O(1)的方式提供消息持久化能力，即使对TB级以上数据也能保证常数时间的访问性能

1. 高吞吐率。即使在非常廉价的商用机器上也能做到单机支持每秒100K条消息的传输

1. 支持Kafka Server间的消息分区，及分布式消费，同时保证每个partition内的消息顺序传输

1. 同时支持离线数据处理和实时数据处理

## 应用场景

1. 数据推送

1. 作为大缓冲区使用

1. 日志收集（scribe或者nginx）

1. 服务中间件

## 工作原理

Kafka 是一个分布式系统，由通过高性能TCP 网络协议进行通信的服务器和客户端组成。它可以部署在内部和云环境中的裸机硬件、虚拟机和容器上。

服务器：Kafka 作为一个或多个服务器的集群运行，这些服务器可以跨越多个数据中心或云区域。其中一些服务器形成存储层，称为代理。其他服务器运行 Kafka Connect以将数据作为事件流持续导入和导出，以将 Kafka 与你现有的系统（例如关系数据库以及其他 Kafka 集群）集成。为了让您实现关键任务用例，Kafka 集群具有高度的可扩展性和容错性：如果其中任何一个服务器出现故障，其他服务器将接管它们的工作，以确保连续运行而不会丢失任何数据。

客户端：它们允许您编写分布式应用程序和微服务，即使在网络问题或机器故障的情况下，它们也可以并行、大规模和容错方式读取、写入和处理事件流。Kafka 附带了一些这样的客户端，这些客户端由 Kafka 社区提供的数十个客户端进行了扩充 ：客户端可用于 Java 和 Scala，包括更高级别的 Kafka Streams库，用于 Go、Python、C/C++ 和许多其他编程语言以及 REST API。

## Kafka 主要概念和术语

1. 生产者Producer

1. 消费者Consumer

1. 主题Topic

1. 分区Partition

1. 副本Replica

1. 偏移量Offset  

# kafka架构

## 从整体来看

从整体来看kafka就是一个消息队列Message Queue。生产者往里面发消息，消费者从里面取消息。

## kafka集群

为了解决三高（高并发、高性能、高可用）问题，我们需要实现kafka集群架构。

## kafka消息主题

为了对kafka消息进行分类，保存在kafka服务器中的数据按照主题（Topic）进行了逻辑分类。

## kafka分区

为了实现负载均衡和水平扩展，Kafka可以将主题划分为多个分区（Partition），会根据分区规则选择把消息存储到哪个分区中，只要如果分区规则设置的合理，那么所有的消息将会被均匀的分布到不同的分区中。

## kafka副本

副本能够对抗部分服务器宕机带来的数据不可用情况。

![](images/WEBRESOURCE0f5a88952f8cccc430dd667e0c9436f1.octet-stream)

# kafka的分区和偏移量

Kafka 中的一切都围绕着分区。它们在构建 Kafka 的存储以及消息的生产和消费方面发挥着至关重要的作用。

# 分区

Kafka 的主题分为几个分区。虽然主题是 Kafka 中的一个逻辑概念，但分区是最小的存储单元 保存主题拥有的记录子集. 每个分区都是一个日志文件，其中记录以仅附加的方式写入其中。

![](images/WEBRESOURCE5dfc772689742ef649ac048e8cc027ae.octet-stream)

# 偏移量和消息排序

分区中的每个记录都分配有一个称为偏移量的顺序标识符，该标识符对于分区内的每个记录都是唯一的。

偏移量是一个增量且不可变的数字，由 Kafka 维护。当一条记录被写入一个分区时，它被附加到日志的末尾，分配下一个顺序偏移量。从分区读取记录时，偏移量对消费者特别有用。我们稍后会谈到这一点。下图显示了一个具有三个分区的主题。记录被附加到每个记录的末尾。

尽管分区内的消息是有序的，但不能保证跨主题的消息是有序的。

![](images/WEBRESOURCE1eb2a654732bc29538c7f174f742ef3b.octet-stream)

Kafka中的一个topic被分成多个partition

# 分区为 Kafka 提供了可扩展性

Kafka 集群由一台或多台服务器组成。在Kafka的世界里，他们被称为broker。每个broker持有属于整个集群的记录子集。

Kafka 将特定主题的分区分布在多个broker之间。通过这样做，我们将获得以下好处。

- 如果我们将一个主题的所有分区放在单个broker中，那么该主题的可扩展性将受到broker的 IO 吞吐量的限制。主题永远不会超过集群中最大的机器。通过在多个broker之间分布分区，可以横向扩展单个主题以提供远远超出单个broker能力的性能。

- 一个主题可以被多个消费者并行消费。从单个broker为所有分区提供服务限制了它可以支持的消费者数量。多个broker上的分区支持更多消费者。

- 同一消费者的多个实例可以连接到不同broker上的分区，从而实现非常高的消息处理吞吐量。每个消费者实例将由一个分区提供服务，确保每条记录都有一个明确的处理所有者。

# 分区是 Kafka 提供冗余的方式

Kafka 跨多个broker保存同一分区的多个副本。这个冗余副本称为副本。如果broker失败，Kafka 仍然可以为消费者提供失败的broker拥有的分区副本。

# 分区策略

生产者如何决定记录应该放在哪个分区？生产者可以通过三种方式对此进行裁决。

## 使用分区键指定分区

生产者可以使用分区键将消息定向到特定分区。分区键可以是可以从应用程序上下文派生的任何值。唯一的设备 ID 或用户 ID 将成为一个很好的分区键。

默认情况下，分区键通过哈希函数传递，该函数创建分区分配。这可确保使用相同键生成的所有记录都将到达同一分区。指定分区键可以将相关事件保存在同一个分区中，并按照它们发送的确切顺序。

![](images/WEBRESOURCE63f73e99aa006cbe052628f9851a6054.octet-stream)

具有相同分区键的消息将在同一个分区结束

如果键没有很好地分布，基于键的分区分配会导致broker偏斜。

例如，当使用客户 ID 作为分区键时，一个客户产生了 90% 的流量，那么大多数时候一个分区将获得 90% 的流量。

选择分区键时，请确保它们分布良好。

## 允许Kafka决定分区

如果生产者在生产记录时没有指定分区键，Kafka 将使用循环分区分配。这些记录将均匀地写入特定主题的所有分区。

但是，如果不使用分区键，则无法保证给定分区内的记录排序。

关键是使用分区键将相关事件按照发送的确切顺序放在同一分区中。

## 编写自定义分区器

在某些情况下，生产者可以使用自己的分区器实现，该实现使用其他业务规则来进行分区分配。

# 从分区读取记录

与其他发布/订阅实现不同，Kafka 不会向消费者推送消息。相反，消费者必须从 Kafka 主题分区中提取消息。消费者连接到broker中的分区，按照消息写入的顺序读取消息。

此时，消息的偏移量用作消费者端游标。消费者通过跟踪消息的偏移量来跟踪它已经消费了哪些消息。读取消息后，消费者将其光标移动到分区中的下一个偏移量并继续。推进和记住分区内的最后一个读取偏移量是使用者的责任。Kafka与它无关。

通过记住每个分区上次消费消息的偏移量，消费者可以在他们选择的时间点加入分区并从那里恢复。这对于消费者在从崩溃中恢复后恢复阅读特别有用。

![](images/WEBRESOURCEce4e78e617006675b901216bd51f894d.octet-stream)

一个分区可以被一个或多个消费者消费，每个消费者在不同的偏移量处读取。                                          

# kafka消费者组

Kafka 有消费者组的概念，其中多个消费者被分组以消费给定的主题。同一消费者组中的消费者被分配相同的group-id值。

消费者组概念确保消息只能由组中的单个消费者读取。

> 
当消费者组消费一个主题的分区时，Kafka 确保每个分区都被组中的一个消费者消费。


![](images/WEBRESOURCE7c7515cb09acbf7c2c9c5da71af3d312.octet-stream)

从多个分区消费的一组消费者。消费者组使消费者能够以非常高的吞吐量并行化和处理消息。但是，一个组的最大并行度将等于该主题的分区数。

例如，如果一个topic有N+1个consumer，有N个partition，那么前N个consumer会被分配一个partition，剩下的consumer会空闲，除非n个consumer中的一个consumer失败，那么等待的consumer会是分配了它的分区。这是实施热故障转移的好策略。

下图说明了这一点。

![](images/WEBRESOURCEd8bc1702469a25d6d00eb207f8d0c221.octet-stream)

关键要点是消费者的数量并不能控制主题的并行度。这是分区的数量

# kafka的下载和安装

## 准备三样东西

1. jdk

https://www.oracle.com/java/technologies/javase/javase8-archive-downloads.html

1. zookeeper压缩包

https://dlcdn.apache.org/zookeeper/

1. kafka压缩包

https://www.apache.org/dyn/closer.cgi?path=/kafka/3.0.0/kafka_2.13-3.0.0.tgz

## 安装jdk

### 下载jdk

```
https://www.oracle.com/java/technologies/javase/javase8-archive-downloads.html

```

### 解压

```
jdk-8u202-linux-i586.tar.gz

```

### 配置环境变量

```
vim /etc/profile
# set java environment
JAVA_HOME=/opt/jdk1.8.0_152
PATH=$PATH:$JAVA_HOME/bin
export JAVA_HOME PATH

```

测试

```
java -version

```

## zookeeper下载安装

### 下载

```
https://dlcdn.apache.org/zookeeper/

```

### 解压

```
tar -zxvf apache-zookeeper-3.7.0-bin.tar.gz  

```

### 修改配置文件

切换到zookeeper的conf目录下面，zoo_sample.cfg 修改为：zoo.cfg，修改zoo.cfg的dataDir=/var/zkdata

> 
数据目录最后自己创建，不使用默认选项


> 
新版kafka已经自带zookeeper，但是建议自己安装zookeeper这样好管理


### 启动zookeeper

```
./zkServer.sh start

```

### 下载安装kafka

```
https://kafka.apache.org/downloads
tar -zxvf kafka_2.13-3.0.0.tgz

```

### 修改日志文件路径

```
log.dirs=/var/kafka-logs

```

> 
路径必须配置否则会启动失败


### 启动kafka

```
bin/kafka-server-start.sh config/server.properties
```

# kafka集群配置

## zookeeper集群环境搭建

### 修改配置文件，添加集群ip

```
server.1=192.168.18.128:2888:3888
server.2=192.168.18.138:2888:3888
server.3=192.168.18.148:2888:3888

```

> 
server.A=B:C:D
A：其中 A 是一个数字，表示这个是服务器的编号；
B：是这个服务器的 ip 地址；
C：Zookeeper服务器之间的通信端口；
D：Leader选举的端口。


### 修改log data 路径

```
dataDir=/var/zkdata

```

### 创建myid

在dataDir目录下面创建myid文件，内容为对应的服务器id 例如 1 2 3

```
echo 1 > /var/zkdata/myid

```

### 查看集群状态

如果一个为leader，另外两个为follower就对了

> 
有时候需要重启服务器 reboot


启动命令：

```
zkServer.sh start

```

查看集群节点状态：

```
zkServer.sh status

```

> 
查看当前服务器是leader 还是follower


## 配置kafka集群

#### 修改server.properties配置文件

1. 修改broker.id ，每台服务器都不一样，最好跟zookeeper myid相同。

1. 修改

log.dirs路径。

1. 修改zookeeper集群路径

```
zookeeper.connect=192.168.18.128:2181,192.168.18.138:2181,192.168.18.148:2181

```

1. 修改host

```
advertised.listeners=PLAINTEXT://192.168.18.128:9092

```

> 
如果zookeeper和kafka都启动过，删除他们的旧的日志，否则会报如下错误


```
ERROR Fatal error during KafkaServer startup. Prepare to shutdown (kafka.server.KafkaServer) kafka.common.InconsistentClusterIdException: The Cluster ID Reu8ClK3TTywPiNLIQIm1w doesn't match stored clusterId Some(BaPSk1bCSsKFxQQ4717R6Q) in meta.properties. The broker is trying to join the wrong cluster. Configured zookeeper.connect may be wrong. at kafka.server.KafkaServer.startup(KafkaServer.scala:220) at kafka.server.KafkaServerStartable.startup(KafkaServerStartable.scala:44) at kafka.Kafka$.main(Kafka.scala:84) at kafka.Kafka.main(Kafka.scala)
```

# kafka manager可视化管理

kafka manager是一个可视化的集群管理软件，使用java编写的web管理界面。下面我们来讲解如何安装配置和使用。

# kafka manager 简介

为了简化开发者和服务工程师维护Kafka集群的工作，yahoo构建了一个叫做Kafka管理器的基于Web工具，叫做 Kafka Manager。这个管理工具可以很容易地发现分布在集群中的哪些topic分布不均匀，或者是分区在整个集群分布不均匀的的情况。它支持管理多个集群、选择副本、副本重新分配以及创建Topic。同时，这个管理工具也是一个非常好的可以快速浏览这个集群的工具，有如下功能：

1. 管理多个kafka集群

1. 便捷的检查kafka集群状态(topics,brokers,备份分布情况,分区分布情况)

1. 选择你要运行的副本

1. 基于当前分区状况进行

1. 可以选择topic配置并创建topic(0.8.1.1和0.8.2的配置不同)

1. 删除topic(只支持0.8.2以上的版本并且要在broker配置中设置delete.topic.enable=true)

1. Topic list会指明哪些topic被删除（在0.8.2以上版本适用）

1. 为已存在的topic增加分区

1. 为已存在的topic更新配置

1. 在多个topic上批量重分区

1. 在多个topic上批量重分区(可选partition broker位置)

## 下载安装

下载地址：https://github.com/yahoo/CMAK/releases

> 
这里我们下载编译好的，而不是源码


## 配置

1. kafka manager 需要jdk11，所以我们需要下载jdk11。

1. 解压缩，修改conf/application.conf配置文件。

```
kafka-manager.zkhosts="192.168.18.128:2181,192.168.18.138:2181,192.168.18.148:2181"

```

1. 启动kafka manager

```
./cmak -java-home /opt/jdk-11.0.12

```

1. 访问，默认端口9000

```
http://192.168.18.128:9000

```

# kafka tool（Offset Explorer）可视化管理kafka

## 下载安装

下载地址：https://www.kafkatool.com/download.html

## 配置

![](images/WEBRESOURCE59b8d2476a73b657f3ad1858569aaf75.octet-stream)

![](images/WEBRESOURCE863c7fc3ff087f6ce688cb658876b652.octet-stream)

> 
修改 C:\Windows\System32\drivers\etc 下面的hosts文件


```
192.168.18.128  node01
192.168.18.138  node02
192.168.18.148  node03
```

# 使用eagle监控kafka

## 下载安装

下载地址：http://download.kafka-eagle.org/

## 安装JDK并配置环境变量

```text
cd /usr/java
tar -zxvf jdk-xxxx.tar.gz
mv jdk-xxxx jdk1.8
vi /etc/profile
export JAVA_HOME=/usr/java/jdk1.8
export PATH=$PATH:$JAVA_HOME/bin

```

> 
这个步骤我们已经配置过


## 解压缩eagle

```
tar -zxvf efak-xxx-bin.tar.gz

rm -rf efak
mv efak-xxx efak

```

## 配置KE_HOME环境变量

```
vi /etc/profile
export KE_HOME=/data/soft/new/efak
export PATH=$PATH:$KE_HOME/bin

```

## 配置eagle

```text
cd ${KE_HOME}/conf
vi system-config.properties

# Multi zookeeper&kafka cluster list -- The client connection address of the Zookeeper cluster is set here
efak.zk.cluster.alias=cluster1
cluster1.zk.list=192.168.18.128:2181,192.168.18.138:2181,192.168.18.148:2181

# Add zookeeper acl
cluster1.zk.acl.enable=false
cluster1.zk.acl.schema=digest
cluster1.zk.acl.username=test
cluster1.zk.acl.password=test123

# Kafka broker nodes online list
cluster1.efak.broker.size=3

# Zkcli limit -- Zookeeper cluster allows the number of clients to connect to
# If you enable distributed mode, you can set value to 4 or 8
kafka.zk.limit.size=16

# EFAK webui port -- WebConsole port access address
efak.webui.port=8048

######################################
# EFAK enable distributed
######################################
efak.distributed.enable=false
# master worknode set status to master, other node set status to slave
efak.cluster.mode.status=slave
# deploy efak server address
efak.worknode.master.host=localhost
efak.worknode.port=8085

# Kafka offset storage -- Offset stored in a Kafka cluster, if stored in the zookeeper, you can not use this option
cluster1.efak.offset.storage=kafka
cluster2.efak.offset.storage=kafka

# Whether the Kafka performance monitoring diagram is enabled
efak.metrics.charts=false

# EFAK keeps data for 30 days by default
efak.metrics.retain=30

# If offset is out of range occurs, enable this property -- Only suitable for kafka sql
efak.sql.fix.error=false
efak.sql.topic.records.max=5000

# Delete kafka topic token -- Set to delete the topic token, so that administrators can have the right to delete
efak.topic.token=keadmin

# Kafka sasl authenticate
cluster1.efak.sasl.enable=false
cluster1.efak.sasl.protocol=SASL_PLAINTEXT
cluster1.efak.sasl.mechanism=SCRAM-SHA-256
cluster1.efak.sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username="admin" password="admin-secret";
# If not set, the value can be empty
cluster1.efak.sasl.client.id=
# Add kafka cluster cgroups
cluster1.efak.sasl.cgroup.enable=false
cluster1.efak.sasl.cgroup.topics=kafka_ads01,kafka_ads02



# Default use sqlite to store data
efak.driver=org.sqlite.JDBC
# It is important to note that the '/hadoop/kafka-eagle/db' path must be exist.
efak.url=jdbc:sqlite:/hadoop/kafka-eagle/db/ke.db
efak.username=root
efak.password=123

# (Optional) set mysql address
#efak.driver=com.mysql.jdbc.Driver
#efak.url=jdbc:mysql://127.0.0.1:3306/ke?useUnicode=true&characterEncoding=UTF-8&zeroDateTimeBehavior=convertToNull
#efak.username=root
#efak.password=smartloli

```

## 启动eagle

```text
cd ${KE_HOME}/bin
chmod +x ke.sh 
ke.sh start

```

### 重启或停止

```text
ke.sh restart
ke.sh stop

```

启动界面如下

![](images/WEBRESOURCEc45fa2da8f1d3423afa0d1d9f8e5f918.octet-stream)



# kafka使用命令行工具管理主题

## 创建topic

使用 kafka-topics.sh脚本

```shell
kafka-topics.sh --create --partitions 3 --replication-factor 2 --topic topic1 --bootstrap-server localhost:9092

```

> 
三个分区，两个副本，主题名称topic1


## 查询所有的topic

使用 kafka-topics.sh脚本

```shell
kafka-topics.sh --list --bootstrap-server localhost:9092

```

## 查看topic的描述信息

使用 kafka-topics.sh脚本

```shell
kafka-topics.sh --describe --topic topic1 --bootstrap-server localhost:9092

```

## 删除topic

使用 kafka-topics.sh脚本

```shell
kafka-topics.sh --delete --topic topic1 --bootstrap-server localhost:9092

```

## 模拟生产者写入数据到topic中

使用 kafka-console-producer.sh 脚本

```shell
kafka-console-producer.sh --broker-list 192.168.18.128:9092,192.168.18.138:9092,192.168.18.148:9092 --topic topic1 

```

## 模拟消费者拉取topic中的数据

使用 kafka-console-consumer.sh 脚本

```shell
kafka-console-consumer.sh --bootstrap-server 192.168.18.128:9092,192.168.18.138:9092,192.168.18.148:9092 --topic topic1 --from-beginning

```

# kafka java生产者客户端

## 创建maven项目

创建maven项目

## 添加依赖

```xml
<dependencies>
    <!-- https://mvnrepository.com/artifact/org.apache.kafka/kafka-clients -->
    <dependency>
        <groupId>org.apache.kafka</groupId>
        <artifactId>kafka-clients</artifactId>
        <version>3.0.0</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/org.apache.kafka/kafka -->
    <dependency>
        <groupId>org.apache.kafka</groupId>
        <artifactId>kafka_2.12</artifactId>
        <version>3.0.0</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/log4j/log4j -->
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/org.slf4j/slf4j-log4j12 -->
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
        <version>1.7.26</version>
        <scope>test</scope>
    </dependency>

    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-nop</artifactId>
        <version>1.7.2</version>
    </dependency

</dependencies>

```

## 实现代码

```java
package com.duoke360;

import org.apache.kafka.clients.producer.*;
import org.apache.kafka.common.serialization.StringSerializer;
import org.apache.log4j.Logger;

import java.util.Properties;


public class Producer {

    static Logger log = Logger.getLogger(Producer.class);

    private static final String TOPIC = "topic1";
    private static final String BROKER_LIST = "node01:9092";
    private static KafkaProducer<String,String> producer = null;

    /*
    初始化生产者
     */
    static {
        Properties configs = initConfig();
        producer = new KafkaProducer<String, String>(configs);
    }

    /*
    初始化配置
     */
    private static Properties initConfig(){
        Properties properties = new Properties();
        properties.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG,BROKER_LIST);
        properties.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG,StringSerializer.class.getName());
        return properties;
    }

    public static void main(String[] args) throws InterruptedException {
        //消息实体
        ProducerRecord<String , String> record = null;
        for (int i = 0; i < 1000; i++) {
            record = new ProducerRecord<String, String>(TOPIC, "value"+(int)(10*(Math.random())));
            //发送消息
            producer.send(record, (recordMetadata, e) -> {
                if (null != e){
                    log.info("send error" + e.getMessage());
                }else {
                    System.out.println(String.format("offset:%s,partition:%s",recordMetadata.offset(),recordMetadata.partition()));
                }
            });
        }
        producer.close();
    }
}
```

# kafka java消费者客户端

## 创建maven项目

创建maven项目

## 添加依赖

```xml
<dependencies>
    <!-- https://mvnrepository.com/artifact/org.apache.kafka/kafka-clients -->
    <dependency>
        <groupId>org.apache.kafka</groupId>
        <artifactId>kafka-clients</artifactId>
        <version>3.0.0</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/org.apache.kafka/kafka -->
    <dependency>
        <groupId>org.apache.kafka</groupId>
        <artifactId>kafka_2.12</artifactId>
        <version>3.0.0</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/log4j/log4j -->
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/org.slf4j/slf4j-log4j12 -->
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
        <version>1.7.26</version>
        <scope>test</scope>
    </dependency>

    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-nop</artifactId>
        <version>1.7.2</version>
    </dependency

</dependencies>

```

## 实现代码

```java
package com.duoke360;

import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.KafkaConsumer;
import org.apache.log4j.Logger;

import java.time.Duration;
import java.util.Arrays;
import java.util.Properties;

public class Consumer {
    static Logger log = Logger.getLogger(Consumer.class);

    private static final String TOPIC = "topic1";
    private static final String BROKER_LIST = "192.168.18.128:9092,192.168.18.138:9092,192.168.18.148:9092";
    private static KafkaConsumer<String,String> consumer = null;

    static {
        Properties configs = initConfig();
        consumer = new KafkaConsumer<String, String>(configs);
        // 订阅主题
        consumer.subscribe(Arrays.asList(TOPIC));
    }

    private static Properties initConfig(){
        Properties properties = new Properties();
        properties.put("bootstrap.servers",BROKER_LIST);
        properties.put("group.id","test");
        properties.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        properties.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        properties.setProperty("enable.auto.commit", "true");
        properties.setProperty("auto.offset.reset", "earliest");
        return properties;
    }


    public static void main(String[] args) {
        while (true) {
            ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(10));
            for (ConsumerRecord<String, String> record : records) {
                log.info(record);
            }
        }
    }
}
```

# kafka python生产者客户端

## 安装库

```
pip install confluent-kafka

```

## 代码

```
from confluent_kafka import Producer

p = Producer({'bootstrap.servers': '192.168.18.128:9092'})

some_data_source = {"java", "python", "golang"}


def delivery_report(err, msg):
    """ Called once for each message produced to indicate delivery result.
        Triggered by poll() or flush(). """
    if err is not None:
        print('Message delivery failed: {}'.format(err))
    else:
        print('Message delivered to {} [{}]'.format(msg.topic(), msg.partition()))


for data in some_data_source:
    # Trigger any available delivery report callbacks from previous produce() calls
    p.poll(0)

    # Asynchronously produce a message, the delivery report callback
    # will be triggered from poll() above, or flush() below, when the message has
    # been successfully delivered or failed permanently.
    p.produce('mytopic', data.encode('utf-8'), callback=delivery_report)

# Wait for any outstanding messages to be delivered and delivery report
# callbacks to be triggered.
p.flush()
```

# kafka python消费者客户端

## 安装库

```
pip install confluent-kafka

```

## 代码

```python
from confluent_kafka import Consumer


c = Consumer({
    'bootstrap.servers': '192.168.18.128:9092',
    'group.id': 'mygroup',
    'auto.offset.reset': 'earliest'
})

c.subscribe(['mytopic'])

while True:
    msg = c.poll(1.0)

    if msg is None:
        continue
    if msg.error():
        print("Consumer error: {}".format(msg.error()))
        continue

    print('Received message: {}'.format(msg.value().decode('utf-8')))

c.close()
```

# kafka golang生产者客户端

### 下载安装kafka Golang客户端

```
go get github.com/Shopify/sarama

```

### 使用Golang创建消息生产者

```go
package main

import (
	"fmt"
	"log"
	"os"
	"time"

	"github.com/Shopify/sarama"
)

var Address = []string{"192.168.18.128:9092"}

func main() {
	syncProducer(Address)
}

//同步消息模式
func syncProducer(address []string) {
	// 配置
	config := sarama.NewConfig()
	// 属性设置
	config.Producer.Return.Successes = true
	config.Producer.Timeout = 5 * time.Second
	// 创建生成者
	p, err := sarama.NewSyncProducer(address, config)
	// 判断错误
	if err != nil {
		log.Printf("sarama.NewSyncProducer err, message=%s \n", err)
		return
	}
	// 最后关闭生产者
	defer p.Close()
	// 主题名称
	topic := "topic1"
	// 消息
	srcValue := "sync: this is a message. index=%d"
	// 循环发消息
	for i := 0; i < 10; i++ {
		// 格式化消息
		value := fmt.Sprintf(srcValue, i)
		// 创建消息
		msg := &sarama.ProducerMessage{
			Topic: topic,
			Value: sarama.ByteEncoder(value),
		}
		// 发送消息
		part, offset, err := p.SendMessage(msg)
		if err != nil {
			log.Printf("send message(%s) err=%s \n", value, err)
		} else {
			fmt.Fprintf(os.Stdout, value+"发送成功，partition=%d, offset=%d \n", part, offset)
		}
		// 每隔两秒发送一个消息
		time.Sleep(2 * time.Second)
	}
}

```

### 命令行客户端接收消息测试

```
bin/kafka-console-consumer.sh --topic quickstart-events --from-beginning --bootstrap-server localhost:9092

```

# kafka golang 消费者客户端

### 下载安装kafka Golang客户端

```
go get github.com/Shopify/sarama

```

### 使用Golang创建消息消费者

```go
package main

import (
	"fmt"
	"time"

	"github.com/Shopify/sarama"
	cluster "github.com/bsm/sarama-cluster"
)

var (
	kafkaConsumer *cluster.Consumer
	kafkaBrokers  = []string{"192.168.18.128:9092"}
	kafkaTopic    = "topic1"
	groupId       = "test_1"
)

func init() {
	// 配置
	var err error
	config := cluster.NewConfig()
	config.Consumer.Return.Errors = true
	config.Group.Return.Notifications = true
	config.Consumer.Group.Rebalance.Strategy = sarama.BalanceStrategyRange
	config.Consumer.Offsets.Initial = -2
	config.Consumer.Offsets.CommitInterval = 1 * time.Second
	config.Group.Return.Notifications = true
	// 创建消费者
	kafkaConsumer, err = cluster.NewConsumer(kafkaBrokers, groupId, []string{kafkaTopic}, config)
	if err != nil {
		panic(err.Error())
	}
	if kafkaConsumer == nil {
		panic(fmt.Sprintf("consumer is nil. kafka info -> {brokers:%v, topic: %v, group: %v}", kafkaBrokers, kafkaTopic, groupId))
	}
	fmt.Printf("kafka init success, consumer -> %v, topic -> %v, ", kafkaConsumer, kafkaTopic)
}

func main() {
	for {
		select {
		case msg, ok := <-kafkaConsumer.Messages():
			if ok {
				fmt.Printf("kafka 接收到的消息: %s \n", msg.Value)
				kafkaConsumer.MarkOffset(msg, "")
			} else {
				fmt.Printf("kafka 监听服务失败")
			}
		case err, ok := <-kafkaConsumer.Errors():
			if ok {
				fmt.Printf("consumer error: %v", err)
			}
		case ntf, ok := <-kafkaConsumer.Notifications():
			if ok {
				fmt.Printf("consumer notification: %v", ntf)
			}
		}
	}
}

```

                                                                    