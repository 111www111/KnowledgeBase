# zookeeper简介

## 简介

zookeeper是apache开源的，使用java开发的，分布式的服务配置管理系统。

## zookeeper功能

1. 存储数据

1. 动态监听

## zookeeper构造机制

基于观察者模式设计的分布式服务管理框架

## zookeeper的存储结构

目录树结构，类似linux文件系统

## 那些软件依赖zookeeper?

1. HBase

1. Spark

1. Flink

1. Storm

1. Kafka

1. Dubbo

## 应用场景

1. 数据发布/订阅

1. 负载均衡

1. 命名服务

1. 分布式协调/通知

1. 集群管理

1. Master 选举

1. 分布式锁

1. 分布式队列

## 官方网站

```
https://zookeeper.apache.org/
```

# zookeeper的特点及数据模型

## Zookeeper的特点

Zookeeper工作在集群中，对集群提供分布式协调服务，它提供的分布式协调服务具有如下的特点：

### 顺序一致性

从同一个客户端发起的事务请求，最终将会严格按照其发起顺序被应用到zookeeper中

### 原子性

所有事物请求的处理结果在整个集群中所有机器上的应用情况是一致的，即，要么整个集群中所有机器都成功应用了某一事务，要么都没有应用，一定不会出现集群中部分机器应用了改事务，另外一部分没有应用的情况。

### 单一视图

无论客户端连接的是哪个zookeeper服务器，其看到的服务端数据模型都是一致的。

### 可靠性

一旦服务端成功的应用了一个事务，并完成对客户端的响应，那么该事务所引起的服务端状态变更将会一直保留下来，除非有另一个事务又对其进行了改变。

### 实时性

zookeeper并不是一种强一致性，只能保证顺序一致性和最终一致性，只能称为达到了伪实时性。

## zookeeper的数据模型

zookeeper中可以保存数据，正是利用zookeeper可以保存数据这一特点，我们的集群通过在zookeeper里存取数据来进行消息的传递。

zookeeper中保存数据的结构非常类似于文件系统。都是由节点组成的树形结构。不同的是文件系统是由文件夹和文件来组成的树，而zookeeper中是由ZNODE来组成的树。

每一个ZNODE里都可以存放一段数据，ZNODE下还可以挂载零个或多个子ZNODE节点，从而组成一个树形结构。       

# zookeeper下载安装

## 下载

```
https://dlcdn.apache.org/zookeeper/

```

## 解压

```
tar -zxvf apache-zookeeper-3.7.0-bin.tar.gz  

```

## 修改配置文件

切换到zookeeper的conf目录下面，zoo_sample.cfg 修改为：zoo.cfg，修改zoo.cfg的dataDir=/var/zkdata

> 
数据目录最后自己创建，不使用默认选项


## 安装jdk

> 
zookeeper 使用java编写的，所以需要配置java运行环境


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

```

```
# set java environment
JAVA_HOME=/opt/jdk1.8.0_152
PATH=$PATH:$JAVA_HOME/bin
export JAVA_HOME PATH

```

测试

```
java -version

```

## 启动服务器

```
./zkServer.sh start

```

## 启动客户端

```
./zkCli.sh 

```

> 
连接到本地服务器，也可以知道ip连击到其他服务器


## 创建节点信息

```
create /site duoke360.com

```

## 获得节点信息

```
get /site
```

# zookeeper客户端命令

## 连接到服务器

```
./zkCli.sh #连接本地的zookeeper服务器
./zkCli.sh -server ip:port #连接指定的服务器

```



## help 查看命令

```
ZooKeeper -server host:port -client-configuration properties-file cmd args
        addWatch [-m mode] path # optional mode is one of [PERSISTENT, PERSISTENT_RECURSIVE] - default is PERSISTENT_RECURSIVE
        addauth scheme auth
        close
        config [-c] [-w] [-s]
        connect host:port
        create [-s] [-e] [-c] [-t ttl] path [data] [acl]
        delete [-v version] path
        deleteall path [-b batch size]
        delquota [-n|-b|-N|-B] path
        get [-s] [-w] path
        getAcl [-s] path
        getAllChildrenNumber path
        getEphemerals path
        history
        listquota path
        ls [-s] [-w] [-R] path
        printwatches on|off
        quit
        reconfig [-s] [-v version] [[-file path] | [-members serverID=host:port1:port2;port3[,...]*]] | [-add serverId=host:port1:port2;port3[,...]]* [-remove serverId[,...]*]
        redo cmdno
        removewatches path [-c|-d|-a] [-l]
        set [-s] [-v version] path data
        setAcl [-s] [-v version] [-R] path acl
        setquota -n|-b|-N|-B val path
        stat [-w] path
        sync path
        version
        whoami

```



## 创建znode

```
create [-s] [-e] path data acl

```



- -s：创建的是带序列号的节点，序列号用0填充节点路径。

- -e：创建的是临时节点。

- path：znode的路径，ZooKeeper中没有相对路径，所有路径都必须以'/'开头。

- data：znode携带的数据。

- acl：这个节点的ACL。

创建一个znode节点

```
create /site duoke360.com

```



创建子节点

```
create /site/course golang

```



创建一个带序列号的znode

```
create -s /node1 666

```



> 
注意，创建了带序列号的znode后，以后只能使用带序列号的路径/node10000000002来引用，而不能用/node1来引用。




创建一个临时znode

```
create -e /temp1 888

```



> 
临时znode在会话退出时会自动删除，所以不能在临时节点上创建子节点。另外临时节点属于某会话，但所有客户端都可以查看、引用它。




## 查看节点的数据和状态信息

```
get /zk_test   // 获取zk_test 节点信息
get /zk_test/child_node1  // 获取zk_test的子节点child_node1信息
stat /zk_test  // 获取zk_test 节点状态信息

```



## 修改节点数据

```
set /zk_test "ghz"

```



## 删除节点

delete可以删除节点。注意它不递归删除，所以要删除一个包含子节点的节点，需要先删除所有子节点：

```
delete /zk_test/zk_child

```



deleteall命令可以递归删除节点，所以也会删除节点中的所有子节点。

```
deleteall site

```



## history和redo

history命令可以列出最近操作的10条命令历史，并给出每个历史命令的编号。

redo命令可以根据历史命令的编号重新调用这些命令。

> 
执行redo 2相当于重新执行get /test10000000002




## connect和close

connect命令用于连接其它ZooKeeper服务器

close用于关闭当前连接。

> 
注意，当connect到其它服务器后，当前的连接就会断开，当前会话中的临时znode就会被删除。执行了close后，不会切换回之前的连接，需要手动再连接connect localhost:2181。




## quit

退出ZooKeeper的交互式命令行            

                            

# zookeeper配置文件

```
# The number of milliseconds of each tick
tickTime=2000
# The number of ticks that the initial
# synchronization phase can take
initLimit=10
# The number of ticks that can pass between
# sending a request and getting an acknowledgement
syncLimit=5
# the directory where the snapshot is stored.
# do not use /tmp for storage, /tmp here is just
# example sakes.
dataDir=/var/zkdata
# the port at which the clients will connect
clientPort=2181
# the maximum number of client connections.
# increase this if you need to handle more clients
#maxClientCnxns=60
#
# Be sure to read the maintenance section of the
# administrator guide before turning on autopurge.
#
# http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance
#
# The number of snapshots to retain in dataDir
#autopurge.snapRetainCount=3
# Purge task interval in hours
# Set to "0" to disable auto purge feature
#autopurge.purgeInterval=1

## Metrics Providers
#
# https://prometheus.io Metrics Exporter
#metricsProvider.className=org.apache.zookeeper.metrics.prometheus.PrometheusMetricsProvider
#metricsProvider.httpPort=7000
#metricsProvider.exportJvmInfo=true

server.1=192.168.18.128:2888:3888
server.2=192.168.18.138:2888:3888
server.3=192.168.18.148:2888:3888

```



zookeeper的默认配置文件为zookeeper/conf/zoo_sample.cfg，需要将其修改为zoo.cfg。其中各配置项的含义，解释如下：

## tickTime

> 
Client-Server通信心跳时间




Zookeeper 服务器之间或客户端与服务器之间维持心跳的时间间隔，也就是每个 tickTime 时间就会发送一个心跳。tickTime以毫秒为单位。

```
tickTime=2000

```



## initLimit

> 
Leader-Follower初始通信时限




集群中的follower服务器(F)与leader服务器(L)之间初始连接时能容忍的最多心跳数（tickTime的数量）。

```
initLimit=5

```



## syncLimit

> 
Leader-Follower同步通信时限




集群中的follower服务器与leader服务器之间请求和应答之间能容忍的最多心跳数（tickTime的数量）。

```
syncLimit=2

```



## dataDir

> 
数据文件目录




Zookeeper保存数据的目录，默认情况下，Zookeeper将写数据的日志文件也保存在这个目录里。

```
dataDir=/var/zk/data

```



## clientPort

> 
客户端连接端口




客户端连接 Zookeeper 服务器的端口，Zookeeper 会监听这个端口，接受客户端的访问请求。

```
clientPort=2181

```



## 服务器名称与地址

集群信息（服务器编号，服务器地址，LF通信端口，选举端口）这个配置项的书写格式比较特殊，规则如下：

```
server.N=YYY:A:B

```



```
A：其中 A 是一个数字，表示这个是服务器的编号；
B：是这个服务器的 ip 地址；
C：Zookeeper服务器之间的通信端口；
D：Leader选举的端口。

```



```
server.1=192.168.18.128:2888:3888
server.2=192.168.18.138:2888:3888
server.3=192.168.18.148:2888:3888

```



## maxClientCnxns

对于一个客户端的连接数限制，默认是60，这在大部分时候是足够了。但是在我们实际使用中发现，在测试环境经常超过这个数，经过调查发现有的团队将几十个应用全部部署到一台机器上，以方便测试，于是这个数字就超了。

## autopurge.snapRetainCount、autopurge.purgeInterval

客户端在与zookeeper交互过程中会产生非常多的日志，而且zookeeper也会将内存中的数据作为snapshot保存下来，这些数据是不会被自动删除的，这样磁盘中这样的数据就会越来越多。

不过可以通过这两个参数来设置，让zookeeper自动删除数据。autopurge.purgeInterval就是设置多少小时清理一次。

而autopurge.snapRetainCount是设置保留多少个snapshot，之前的则删除。                           

# zookeeper服务器常用命令

## 启动服务

```
./zkServer.sh   start

```

> 
不指定配置文件启动zk服务


```
./zkSever.sh start ../conf/zoo.cfg

```

> 
指定配置文件启动zk服务


```
./zkServer.sh start-foreground

```

> 
在当前会话窗口启动zookeeper服务，日志会在当前窗口打印，使用ctrl+c直接关闭服务


## 重启服务

```
./zkServer.sh restart

```

> 
重启zookeeper服务，服务自己先关闭，然后自己再启动。


## 停止服务

```
./zkServer.sh stop

```

> 
停止当前zookeeper服务


## 查看服务状态

```
./zkServer.sh status

```

查看服务器状态信息，显示该服务器使用哪个配置启动，该服务器得模式是Leader还是follower。

## 打印服务器java配置信息

```
./zkServer.sh print-cmd

```

## 服务器升级

```
./zkServer.sh upgrade

```

# zookeeper可视化管理工具zkui

## 下载

```
https://github.com/DeemOpen/zkui

```

## 修改配置文件

```
mvn clean install

```

> 
修改 config.cfg  zkServer=192.168.18.128:2181, 和zkSessionTimeout=20 这个值修改的大一些否则会连接失败！


## 运行

1. 拷贝config.cfg到target目录下面

1. 运行 java -jar zkui-2.0-SNAPSHOT-jar-with-dependencies.jar &

1. 访问 localhost:9090 用户名 admin 密码 manager

                            

# zookeeper集群环境搭建

## 修改配置文件，添加集群ip

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


## 修改log data 路径

```
dataDir=/var/zkdata

```

## 创建myid

在dataDir目录下面创建myid文件，内容为对应的服务器id 例如 1 2 3

```
echo 1 > /var/zkdata/myid

```

## 查看集群状态

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


# zookeeper选举机制

## CAP理论

CAP原则又称CAP定理，指的是在一个分布式系统中，一致性（Consistency）、可用性（Availability）、分区容错性（Partition tolerance）。CAP 原则指的是，这三个要素最多只能同时实现两点，不可能三者兼顾。

## CAP分别是什么？

- 一致性(Consistency) (所有节点在同一时间具有相同的数据)

- 可用性(Availability) (保证每个请求不管成功或者失败都有响应)

- 分区容错性(Partition tolerance) (系统中任意信息的丢失或失败不会影响系统的继续运作)

## CAP原理简单证明

假设有节点data1和节点data2，一开始有个数据number=1。之后向data1提交更新，将数据number设置为2。

接着data1就需要将更新推送给data2，让data2也更新number数据。

接下来我们分3个场景分析：

1. 在保证C和P的情况下
为了保证数据一致性，data1需要将数据复制给data2，即data1和data2需要进行通信。但是由于网络是不可靠的，我们系统有保证了分区容忍性，也就是说这个系统是可以容忍网络的不可靠的。这时候data2就不一定能及时的收到data1的数据复制消息，当有请求向data2访问number数据时，为了保证数据的一致性，data2只能阻塞等待数据真正同步完成后再返回，这时候就没办法保证高可用性了。所以，在保证C和P的情况下，是无法同时保证A的。

1. 在保证A和P的情况下为了保证高可用性，data1和data2都有在有限时间内返回。同样由于网络的不可靠，在有限时间内，data2有可能还没收到data1发来的数据更新消息，这时候返回给客户端的可能是旧的数据，和访问data1的数据是不一致的，也就是违法了C。也就是说，在保证A和P的情况下，是无法同时保证C的。

1. 在保证A和C的情况下如果要保证高可用和一致性，只有在网络情况良好且可靠的情况下才能实现。这样data1才能立即将更新消息发送给data2。但是我们都知道网络是不可靠的，是会存在丢包的情况的。所以要满足即时可靠更新，只有将data1和data2放到一个区内才可以，也就丧失了P这个保证。其实这时候整个系统也不能算是一个分布式系统了。

## zookeeper选举机制

在Zookeeper中，分为两种选举情况

- 一种是初始化集群分布式的时候会进行leader选举，

- 另外一种是在运行期间leader出现故障会进行选举。

一般Zookeeper的服务器机器数量都会选择为奇数。在分布式CAP理论中，zookeeper属于一个CP系统，即一致性、分区容错性，它保证了集群数据的一致性，但适当舍弃了一些高可用。

zookeeper节点的4种状态:

- LEADING：说明此节点已经是leader节点，处于领导者地位的状态，差不多就是一般集群中的master。但在zookeeper中，只有leader才有写权限，其他节点（FOLLOWING）是没有写权限的，可以读

- LOOKING：选举中，正在寻找leader，即将进入leader选举流程中。

- FOLLOWING：跟随者，表示当前集群中的leader已经选举出来了，主要具备以下几个功能点 
向leader发送请求（PING消息、REQUEST消息、ACK消息、REVALIDATE消息）接收leader消息并进行处理；接收client发送过来的请求，如果为写请求，会发送给Leader进行投票处理，然后返回client结果。

- OBSERVING：OBSERVING和FOLLOWING差不多，但不参加投票和选举，接受leader选举后的结果

## 选举过程

假如有以下5台机器server1、server2、server3、server4、server5

每个server 自身都有一票，在初始化或者server崩溃数过半的时候，每个server都有一个自身的myid(zookeeper配置文件)，这里按1、2、3、4、5算在选举过程中主要是依据zxid和myid来进行轮训server然后比较统计投票zxid (ZooKeeper Transaction Id，每次请求对应一个唯一的zxid，如果zxid a < zxid b ，则可以保证a一定发生在b之前)。

选举分为两种情况，初始化和leader挂掉的时候，要进行leader选举，至少需要2台机器，集群机器台数基本是奇数

### 初始化

当启动初始化集群的时候，server1的myid为1，zxid为0   server2的myid为2，zxid同样是0，以此类推。此种情况下zxid都是为0。先比较zxid，再比较myid服务器1启动，给自己投票，然后发投票信息，由于其它机器还没有启动所以它收不到反馈信息，服务器1的状态一直属于Looking(选举状态)。

服务器2启动，给自己投票，同时与之前启动的服务器1交换结果，由于服务器2的myid大所以服务器2胜出，但此时投票数没有大于半数，所以两个服务器的状态依然是LOOKING。

服务器3启动，给自己投票，同时与之前启动的服务器1,2交换信息，由于服务器3的myid最大所以服务器3胜出，此时投票数正好大于半数，所以服务器3成为领导者，服务器1,2成为小弟。

服务器4启动，给自己投票，同时与之前启动的服务器1,2,3交换信息，尽管服务器4的myid大，但之前服务器3已经胜出，所以服务器4只能成为小弟。

服务器5启动，后面的逻辑同服务器4成为小弟当选举机器过半的时候，已经选举出leader后，后面的就跟随已经选出的leader，所以4和5跟随成为leader的server3

所以，在初始化的时候，一般到过半的机器数的时候谁的myid最大一般就是leader

### 运行期间

按照上述初始化的情况，server3成为了leader，在运行期间处于leader的server3挂了，那么非Observer服务器server1、server2、server4、server5会将自己的节点状态变为LOOKING状态

1. 开始进行leader选举。现在选举同样是根据myid和zxid来进行



1. 首先每个server都会给自己投一票竞选leader。假设server1的zxid为123，server2的zxid为124，server4的zxid为169，server5的zxid为188



1. 同样先是比较zxid再比较，server1、server2、server4比较server4根据优先条件选举为leader。然后server5还是跟随server4，即使server5的zxid最大，但是当选举到server4的时候，机器数已经过半。不再进行选举，跟随已经选举的leader



zookeeper集群为保证数据的一致性所有的操作都是由leader完成，之后再由leader同步给follower。重点就在这儿，zookeeper并不会确保所有节点都同步完数据，只要有大多数节点（即n/2+1）同步成功即可。

咱们假设有一个写操作成功那么现在数据只存在于节点leader，之后leader再同步给其他follower。这时候宕掉3个机器，已经过半的机器无法进行投票选举，剩余2台不足过半，无法选举=无法提供任何服务。再启动一个机器恢复服务。所以宕掉的机器不要过半，过半就会导致无法正常服务。        

# 使用Java原生api操作zookeeper

## 创建一个maven项目

创建一个maven项目

## 添加项目依赖

```xml
<dependencies>
    <dependency>
        <groupId>org.apache.zookeeper</groupId>
        <artifactId>zookeeper</artifactId>
        <version>3.4.8</version>
    </dependency>
</dependencies>

```

> 
https://mvnrepository.com/


## 实现代码

```java
package com.duoke360;

import org.apache.zookeeper.*;

import java.io.IOException;
import java.util.concurrent.CountDownLatch;

public class Test {
    public static void main(String[] args) throws IOException, InterruptedException, KeeperException {
        String connStr = "192.168.18.128:2181";
        CountDownLatch countDown = new CountDownLatch(1);

        Watcher watcher= event -> {
            if (event.getState() == Watcher.Event.KeeperState.SyncConnected) {
                System.err.println("eventType:"+event.getType());
                if(event.getType()== Watcher.Event.EventType.None){
                    countDown.countDown();
                }else if(event.getType()== Watcher.Event.EventType.NodeCreated){
                    System.out.println("listen:节点创建");
                }else if(event.getType()== Watcher.Event.EventType.NodeChildrenChanged){
                    System.out.println("listen:子节点修改");
                }
            }
        };

        ZooKeeper zookeeper = new ZooKeeper(connStr, 5000,watcher);
        countDown.await();

        //注册监听,每次都要重新注册，否则监听不到
        // 先创建一个根节点root
        zookeeper.exists("/root/ghz", watcher);

        // 创建节点
        String result = zookeeper.create("/root/ghz", "老郭".getBytes(),
                ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.PERSISTENT);
        System.out.println(result);

        Thread.sleep(10);

        // 获取节点
        byte[] bs = zookeeper.getData("/root/ghz", true, null);
        result = new String(bs);
        System.out.println("创建节点后的数据是:" + result);

        // 修改节点
        zookeeper.setData("/root/ghz", "多课网-老郭".getBytes(), -1);

        Thread.sleep(10);

        bs = zookeeper.getData("/root/ghz", true, null);
        result = new String(bs);


        System.out.println("修改节点后的数据是:" + result);
        // 删除节点
        zookeeper.delete("/root/ghz", -1);
        System.out.println("节点删除成功");
    }
}

```

## 运行结果

```
eventType:None
eventType:NodeCreated
listen:节点创建
/root/ghz
创建节点后的数据是:老郭
eventType:NodeDataChanged
修改节点后的数据是:多课网-老郭
节点删除成功
eventType:NodeDeleted
```

# 使用java zkclient库操作zookeeper

## 创建一个maven项目

创建一个maven项目

## 添加依赖

```xml
<!-- https://mvnrepository.com/artifact/com.101tec/zkclient -->
<dependency>
    <groupId>com.101tec</groupId>
    <artifactId>zkclient</artifactId>
    <version>0.10</version>
</dependency>

```

## 实现代码

```java
package com.duoke360;

import org.I0Itec.zkclient.IZkDataListener;
import org.I0Itec.zkclient.ZkClient;
import org.apache.zookeeper.*;

import java.util.List;

public class Test {
    public static void main(String[] args) throws InterruptedException {
        String connStr = "192.168.18.128:2181";
        ZkClient zk = new ZkClient(connStr);

        // 注册【数据】事件
        zk.subscribeDataChanges("/root/ghz", new IZkDataListener() {

            @Override
            public void handleDataDeleted(String arg0) throws Exception {
                System.err.println("数据删除:" + arg0);

            }

            @Override
            public void handleDataChange(String arg0, Object arg1) throws Exception {
                System.err.println("数据修改:" + arg0 + "------" + arg1);

            }
        });

        zk.subscribeChildChanges("/root", (arg0, arg1) -> {
            System.err.println("子节点发生变化：" + arg0);
            arg1.forEach(f -> {
                System.out.println("content：" + f);
            });
        });

        List<String> list = zk.getChildren("/");
        list.forEach(e -> {
            System.out.println(e);
        });

        String res = zk.create("/root/ghz", "多课网-老郭", CreateMode.PERSISTENT);
        System.out.println("创建节点/root/ghz成功:" + res);

        zk.writeData("/root/ghz", "多课网-zookeeper");
        System.out.println("修改节点/root/ghz数据成功");

        res = zk.readData("/root/ghz");
        System.out.println("节点数据:" + res);

        Thread.sleep(1000);

        zk.delete("/root/ghz");
        System.out.println("删除节点/root/ghz成功");

        Thread.sleep(1000);

        System.out.println("------------------------------------------------");

        for (int i = 0; i < 10; i++) {
            zk.create("/root/ghz", "多课网-老郭", CreateMode.PERSISTENT);
            Thread.sleep(1000);
            zk.delete("/root/ghz");
            Thread.sleep(1000);
        }
    }
}

```

## 运行结果

```
node20000000002
zookeeper
node10000000001
root
创建节点/root/ghz成功:/root/ghz
修改节点/root/ghz数据成功
节点数据:多课网-zookeeper
content：ghz
数据修改:/root/ghz------多课网-zookeeper
子节点发生变化：/root
数据修改:/root/ghz------多课网-zookeeper
删除节点/root/ghz成功
数据删除:/root/ghz
子节点发生变化：/root
------------------------------------------------
数据修改:/root/ghz------多课网-老郭
子节点发生变化：/root
content：ghz
数据删除:/root/ghz
子节点发生变化：/root
数据修改:/root/ghz------多课网-老郭
子节点发生变化：/root
content：ghz
数据删除:/root/ghz
子节点发生变化：/root
数据修改:/root/ghz------多课网-老郭
子节点发生变化：/root

```

# 使用Java curator库操作zookeeper

## 创建一个maven项目

创建一个maven项目

## 添加依赖

```xml
<!-- https://mvnrepository.com/artifact/org.apache.curator/curator-recipes -->
<dependency>
    <groupId>org.apache.curator</groupId>
    <artifactId>curator-recipes</artifactId>
    <version>4.2.0</version>
</dependency>

```

## 实现代码

```java
package com.duoke360;

import org.apache.curator.framework.CuratorFramework;
import org.apache.curator.framework.CuratorFrameworkFactory;
import org.apache.curator.framework.recipes.cache.PathChildrenCache;
import org.apache.curator.framework.recipes.cache.PathChildrenCacheEvent;
import org.apache.curator.framework.recipes.cache.PathChildrenCacheListener;
import org.apache.curator.retry.ExponentialBackoffRetry;
import org.apache.zookeeper.CreateMode;
import org.apache.zookeeper.data.Stat;

public class Test {
    public static void main(String[] args) throws Exception {
        String connStr = "192.168.18.128:2181";
        CuratorFramework cur= CuratorFrameworkFactory.builder()
                .connectString(connStr)
                .connectionTimeoutMs(5000)
                .retryPolicy(new ExponentialBackoffRetry(1000,3))
                .build();
        cur.start();//连接

        //创建监听
        PathChildrenCache cache=new PathChildrenCache(cur,"/root",true);
        cache.start();
        cache.rebuild();
        cache.getListenable().addListener(new PathChildrenCacheListener() {
            @Override
            public void childEvent(CuratorFramework framwork, PathChildrenCacheEvent event) throws Exception {
                System.err.println("节点发生变化:"+event.getType());
            }
        });

        Stat stat=cur.checkExists().forPath("/root/ghz");
        if(stat!=null){
            System.out.println("/root/ghz 节点存在，直接删除");
            cur.delete().forPath("/root/ghz");
        }

        System.in.read();

        System.out.println("准备创建 /root/ghz");
        cur.create().withMode(CreateMode.PERSISTENT)
                .forPath("/root/ghz", "多课网-老郭".getBytes());
        System.out.println("节点 /root/ghz 创建成功");

        Thread.sleep(1000);

        byte[] bs=cur.getData().forPath("/root/ghz");
        System.out.println("数据:"+new String(bs));

        Thread.sleep(1000);

        cur.delete().forPath("/root/ghz");

        Thread.sleep(1000);
    }
}

```

## 运行结果

```
test
准备创建 /root/ghz
节点 /root/ghz 创建成功
节点发生变化:CHILD_ADDED
数据:多课网-老郭
节点发生变化:CHILD_REMOVED

```

# 使用Python操作zookeeper

### 安装

```python
pip install kazoo

```

### 连接 ZooKeeper

可通过 KazooClient 类直接连接 ZooKeeper ，支持多个 host ，端口默认 2181。

```python
import json
from kazoo.client import KazooClient

zk = KazooClient(hosts='192.168.18.128:2181')
zk.start()

```

### 创建节点

先看下 create() 方法定义

```python
def create(self, path, value=b"", acl=None, ephemeral=False,
               sequence=False, makepath=False):
        :param path: Path of node.
        :param value: Initial bytes value of node.
        :param acl: :class:`~kazoo.security.ACL` list.
        :param ephemeral: Boolean indicating whether node is ephemeral
                          (tied to this session).
        :param sequence: Boolean indicating whether path is suffixed
                         with a unique index.
        :param makepath: Whether the path should be created if it
                         doesn't exist.

```

我们来解释下这些参数：

- path：     节点路径

- value：     节点对应的值，注意值的类型是 bytes

- ephemeral： 若为 True 则创建一个临时节点，session 中断后自动删除该节点。默认 False

- sequence:   若为 True 则在你创建节点名后面增加10位数字（例如：你创建一个 testplatform/test 节点，实际创建的是 testplatform/test0000000003，这串数字是顺序递增的）。默认 False

- makepath： 若为 False 父节点不存在时抛 NoNodeError。若为 True 父节点不存在则创建父节点。默认 False

实例：

```python
from kazoo.client import KazooClient

zk = KazooClient(hosts='192.168.18.128:2181')
zk.start()
# 创建节点：makepath 设置为 True ，父节点不存在则创建，其他参数不填均为默认
zk.create('/root/ghz',b'',makepath=True)
# 操作完后，别忘了关闭zk连接
zk.stop()

```

### 查看节点

KazooClient 类用提供 get_children() 和 get() 方法获取 子节点 和 节点对应的值

```python
from kazoo.client import KazooClient

zk = KazooClient(hosts='192.168.18.128:2181')
zk.start()
# 获取某个节点下所有子节点
node = zk.get_children('/root')
# 获取某个节点对应的值
value = zk.get('/root/ghz')
# 操作完后，别忘了关闭zk连接
zk.stop()
print(node,value)

```

### 更改节点

更改上文创建的 node 值，使用 set() 方法

```python
from kazoo.client import KazooClient

zk = KazooClient(hosts='192.168.18.128:2181')
zk.start()
# 更改节点对应的value
zk.set('/root/ghz', b'duoke-ghz')
# 获取某个节点对应的值
value = zk.get('/root/ghz')
zk.stop()
print(value)

```

### 删除节点

删除上文创建的节点，使用 delete() 方法

```python
from kazoo.client import KazooClient

zk = KazooClient(hosts='192.168.18.128:2181')
zk.start()
# 删除节点对应的value
zk.delete('/root/ghz',recursive=False)
zk.stop()

```

> 
参数 recursive：若为 False，当需要删除的节点存在子节点，会抛异常 NotEmptyError 。若为True，则删除 此节点 以及 删除该节点的所有子节点


### watches 事件

zookeeper 所有读操作都有设置 watch 选项（get_children() 、get() 和 exists()）。watch 是一个触发器，当检测到 zookeeper 有子节点变动 或者 节点value发生变动时触发。下面以 get() 方法为例。

```python
from kazoo.client import KazooClient

zk = KazooClient(hosts='192.168.18.128:2181')
zk.start()


def test(event):
    print('触发事件')


if __name__ == "__main__":
    # 节点必须存在，否则保存
    zk.get('/root/ghz', watch=test)
    print("第一次获取value")
    zk.set('/root/ghz', b'duoke-ghz')
    zk.get('/root/ghz', watch=test)
    zk.set('/root/ghz', b'duoke-ghz2')
    print("第二次获取value")

    zk.stop()

```

### 运行结果

```
第一次获取value
触发事件
第二次获取value

```

# 使用golang操作zookeeper

## 下载包

```
go get github.com/samuel/go-zookeeper

```

## 连接到Server

```go
package main

import (
	"fmt"
	"time"

	"github.com/samuel/go-zookeeper/zk"
)

func conn() *zk.Conn {
	var hosts = []string{"192.168.18.128:2181"}
	conn, _, err := zk.Connect(hosts, time.Second*5)
	defer conn.Close()
	if err != nil {
		fmt.Println(err)
		return nil
	} else {
		fmt.Println("连接成功！")
		return conn
	}
}

func main() {
	conn()
}

```

## 创建节点

```go
func create() {
	var conn *zk.Conn = conn()
	defer conn.Close()
	var path = "/home"
	var data = []byte("多课网")
	var flags int32 = 0
	//flags有4种取值：
	//0:永久，除非手动删除
	//zk.FlagEphemeral = 1:短暂，session断开则改节点也被删除
	//zk.FlagSequence  = 2:会自动在节点后面添加序号
	//3:Ephemeral和Sequence，即，短暂且自动添加序号
	var acls = zk.WorldACL(zk.PermAll) //控制访问权限模式

	p, err_create := conn.Create(path, data, flags, acls)
	if err_create != nil {
		fmt.Println(err_create)
		return
	}
	fmt.Println("create:", p)
}

```

## 修改节点

```go
func set() {
	var conn *zk.Conn = conn()
	defer conn.Close()
	var path = "/home"
	var data = []byte("多课网-老郭")
	conn.Set(path, data, -1)
	b, _, _ := conn.Get(path)
	fmt.Println("数据：" + string(b))
}

```

## 删除节点

```go
func del() {
	var conn *zk.Conn = conn()
	defer conn.Close()
	var path = "/home"
	err := conn.Delete(path, -1)
	if err != nil {
		fmt.Println("删除失败！")
	} else {
		fmt.Println("删除成功！")
	}
}

```

## watch

```go
func callback(event zk.Event) {
	fmt.Println("*******************")
	fmt.Println("path:", event.Path)
	fmt.Println("type:", event.Type.String())
	fmt.Println("state:", event.State.String())
	fmt.Println("*******************")
}

func watch() {

	var hosts = []string{"192.168.18.128:2181"}
	option := zk.WithEventCallback(callback)

	conn, _, err := zk.Connect(hosts, time.Second*5, option)
	defer conn.Close()
	if err != nil {
		fmt.Println(err)
		return
	}
	var path = "/home"
	_, _, _, err = conn.ExistsW(path)
	if err != nil {
		fmt.Println(err)
		return
	}

	// 创建
	create(conn)

	time.Sleep(time.Second * 2)

	_, _, _, err = conn.ExistsW(path)
	if err != nil {
		fmt.Println(err)
		return
	}
	// 删除
	del(conn)

}
```

使用ZooInspector工具可视化管理zookeeper

## 下载ZooInspector

下载地址：https://issues.apache.org/jira/secure/attachment/12436620/

## 运行ZooInspector

进入目录ZooInspector\build，运行zookeeper-dev-ZooInspector.jar；

```
java -jar zookeeper-dev-ZooInspector.jar &

```

## 使用JMX监控zookeeper

## 科普JMX

JMX（Java Management Extensions，即Java管理扩展）是一个为应用程序、设备、系统等植入管理功能的框架。JMX可以跨越一系列异构操作系统平台、系统体系结构和网络传输协议，灵活的开发无缝集成的系统、网络和服务管理应用。

## 使用JMX监控zookeeper

1. 在zookeeper conf目录下面创建一个文件

zookeeper-env.sh，内容如下：

```
JMXLOCALONLY=false
JMXDISABLE=false
JMXPORT=4048
JMXAUTH=false
JMXSSL=false

```

1. 重新启动zookeeper。

1. 客户端使用jconsole连接，使用ip+port 访问

# zookeeper四字监控命令

## 什么是四字监控命令

zookeeper支持某些特定的四字命令与其的交互。它们大多数是查询命令，用来获取zookeeper服务的当前状态及相关信息。用户在客户端可以通过telnet或nc向zookeeper提交相应的命令。

zookeeper常用四字命令见下表所示：

| 使用命令 | 输出说明 |
| - | - |
| echo conf | nc 192.168.18.128  2181 | 输出Zookeeper相关服务的详细配置信息，如客户端端口，数据存储路径、最大连接数、日志路径、数据同步端口、主节点推举端口、session超时时间等等 |
| echo cons | nc 192.168.18.128  2181 | 输出连接到Zookeeper的服务器信息，包括队列、数据接收量、数据发送量、sessionID、操作延时、最后的操作信息等等 |
| echo crst | nc 192.168.18.128  2181 | 重置当前的所有连接、会话等等 |
| echo dump | nc 192.168.18.128  2181 | 列出未经处理的会话和连接信息 |
| echo envi | nc 192.168.18.128 2181 | 列出zookeeper的版本信息、主机名称、Java版本、java\_home、class\_path、服务器名称、当前登陆用户名、目录等等； |
| echo ruok | nc 192.168.18.128 2181 | Are You Ok的缩写，测试服务器是否正在运行，如果在运行返回imok，否则返回空 |
| echo srst | nc 192.168.18.128 2181 | 重置Zookeeper的所有统计信息 |
| echo srvr | nc 192.168.18.128 2181 | 列出zookeeper的版本信息、数据接收/发送量、连接数、节点模式、Node数、最大/平均/最小延迟数 |
| echo stat | nc 192.168.18.128 2181 | 列出连接客户端列表、最大/平均/最小延迟数、数据接收/发送量、连接数、节点总数、模式 |
| echo wchs | nc 192.168.18.128 2181 | 列出watch的总数，连接数 |
| echo wchp | nc 192.168.18.128 2181 | 列出所有watch的路径及sessionID |
| echo mntr | nc 192.168.18.128 2181 | 列出集群的关键性能数据，包括zk的版本、最大/平均/最小延迟数、数据包接收/发送量、连接数、zk角色（Leader/Follower）、node数量、watch数量、临时节点数 |


## 配置监控属性

1. 在zoo.cfg中添加如下一行代码并保存退出



```
#开启四字命令
4lw.commands.whitelist=*

```

1. 重启zookeeper



```
./zkServer.sh  restart

```

## 安装nc

```
yum install -y nc

```

# conf命令

输出相关服务配置的详细信息

| 属性 | 含义 |
| - | - |
| clientPort | 客户端端口号 |
| dataDir | 数据快照文件目录，默认情况下<br>10w<br>次事务操作生成一次快照 |
| dataLogDir | 事务日志文件目录，生产环节中放再独立的磁盘上 |
| tickTime | 服务器之间或客户端与服务器之间维持心跳的时间间隔(以毫秒为单位) |
| maxClientCnxns | 最大连接数 |
| minSessionTimeout | 最小<br>session<br>超时<br>minSessionTimeout=tickTime\*2<br> ，即使客户端连接设置了会话超时，也不能打破这个限制 |
| maxSessionTimeout | 最大<br>session<br>超时<br>maxSessionTimeout=tickTime\*20<br>，即使客户端连接设置了会话超时，也不能打破这个限制 |
| serverId | 服务器编号 |
| initLimit | 集群中<br>follower<br>服务器<br>(F)<br>与<br>leader<br>服务器<br>(L)<br>之间初始连接时能容忍的最多心跳数，实际上以<br>tickTime<br>为单位，换算为毫秒数 |
| syncLimit | 集群中<br>follower<br>服务器<br>(F)<br>与<br>leader<br>服务器<br>(L)<br>之间请求和应答之间能容忍的最大心跳数，实际上以<br>tickTime<br>为单位，换算为毫秒数 |
| electionAlg | 0：基于<br>UDP<br>的<br>LeaderElection<br>1：基于<br>UDP<br>的<br>FastLeaderElection<br>2：基于UDP和认证的<br>FastLeaderElection<br>3：基于<br>TCP<br>的<br>FastLeaderElection<br>在<br>3.4.10<br>版本中，默认值为3，另外三种算法以及被弃用，并且有计划在之后的版本中将它们彻底删除且不再支持 |
| electionPort | 选举端口 |
| quorumPort | 数据通信端口 |
| peerType | 是否为观察者 1为观察者 |


```
echo conf | nc 192.168.18.128  2181

# 输出
clientPort=2181
secureClientPort=-1
dataDir=/var/zkdata/version-2
dataDirSize=402655980
dataLogDir=/var/zkdata/version-2
dataLogSize=402655980
tickTime=2000
maxClientCnxns=60
minSessionTimeout=4000
maxSessionTimeout=40000
clientPortListenBacklog=-1
serverId=1
initLimit=10
syncLimit=5
electionAlg=3
electionPort=3888
quorumPort=2888
peerType=0
membership:
server.1=192.168.18.128:2888:3888:participant
server.2=192.168.18.138:2888:3888:participant
server.3=192.168.18.148:2888:3888:participant

```

# cons命令详解

列出所有连接到这台服务器的客户端连接/会话的详细信息

| 属性 | 含义 |
| - | - |
| ip | IP地址 |
| port | 端口号 |
| queued | 等待被处理的请求数，请求缓存在队列中 |
| received | 收到的包数 |
| sent | 发送的包数 |
| sid | 会话id |
| lop | 最后的操作 GETD-读取数据 DELE-删除数据 CREA-创建数据 |
| est | 连接时间戳 |
| to | 超时时间 |
| lcxid | 当前会话的操作id |
| lzxid | 最大事务id |
| lresp | 最后响应时间戳 |
| llat | 最后/最新 延迟 |
| minlat | 最小延时 |
| maxlat | 最大延时 |
| avglat | 平均延时 |


```
echo cons | nc 192.168.18.128  2181

# 输出
/192.168.18.128:54850[0](queued=0,recved=1,sent=0)
 /127.0.0.1:55328[1](queued=0,recved=4,sent=4,sid=0x10009cd9b510000,lop=PING,est=1640760120876,to=30000,lcxid=0x0,lzxid=0x600000001,lresp=165254084,llat=0,minlat=0,avglat=3,maxlat=14)


```

# crst命令

此命令用于重置当前这台服务器所有连接/会话的统计信息 。

```
echo crst | nc 192.168.18.128  2181
# 输出
Connection stats reset.

```

## dump命令

列出未经处理的临时节点，适用于leader

```
echo dump | nc 192.168.18.128  2181
# 输出
SessionTracker dump:
Global Sessions(1):
0x10009cd9b510000       30000ms
ephemeral nodes dump:
Sessions with Ephemerals (0):
Connections dump:
Connections Sets (4)/(2):
0 expire at Wed Dec 29 14:46:16 CST 2021:
1 expire at Wed Dec 29 14:46:26 CST 2021:
        ip: /192.168.18.148:52580 sessionId: 0x0
0 expire at Wed Dec 29 14:46:36 CST 2021:
1 expire at Wed Dec 29 14:46:46 CST 2021:
        ip: /127.0.0.1:55328 sessionId: 0x10009cd9b510000

```

# envi命令

输出关于服务器的环境详细信息

| 属性 | 含义 |
| - | - |
| zookeeper.version | 版本 |
| host.name | host<br>信息 |
| java.version | java<br>版本 |
| java.vendor | 供应商 |
| java.home | 运行环境所在目录 |
| java.class.path | classpath |
| java.library.path | 第三方库指定非Java类包的为止(如：dll，so) |
| java.io.tmpdir | 默认的临时文件路径 |
| java.compiler | JIT<br>编辑器的名称 |
| os.name | Linux |
| os.arch | amd64 |
| os.version | 3.10.0-1062.el7.x86\_64 |
| user.name | zookeeper |
| user.home | /opt/zookeeper |
| user.dir | /opt/zookeeper/zookeeper2181/bin |


```
echo envi | nc 192.168.18.128  2181
# 输出
Environment:
zookeeper.version=3.7.0-e3704b390a6697bfdf4b0bef79e3da7a4f6bac4b, built on 2021-03-17 09:46 UTC
host.name=node01
java.version=1.8.0_292
java.vendor=Red Hat, Inc.
java.home=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.292.b10-1.el7_9.x86_64/jre
java.class.path=/opt/apache-zookeeper-3.7.0-bin/bin/../zookeeper-server/target/classes:/opt/apache-zookeeper-3.7.0-bin/bin/../build/classes:/opt/apache-zookeeper-3.7.0-bin/bin/../zookeeper-server/target/lib/*.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../build/lib/*.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/zookeeper-prometheus-metrics-3.7.0.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/zookeeper-jute-3.7.0.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/zookeeper-3.7.0.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/snappy-java-1.1.7.7.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/slf4j-log4j12-1.7.30.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/slf4j-api-1.7.30.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/simpleclient_servlet-0.9.0.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/simpleclient_hotspot-0.9.0.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/simpleclient_common-0.9.0.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/simpleclient-0.9.0.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/netty-transport-native-unix-common-4.1.59.Final.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/netty-transport-native-epoll-4.1.59.Final.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/netty-transport-4.1.59.Final.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/netty-resolver-4.1.59.Final.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/netty-handler-4.1.59.Final.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/netty-common-4.1.59.Final.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/netty-codec-4.1.59.Final.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/netty-buffer-4.1.59.Final.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/metrics-core-4.1.12.1.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/log4j-1.2.17.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/jline-2.14.6.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/jetty-util-ajax-9.4.38.v20210224.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/jetty-util-9.4.38.v20210224.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/jetty-servlet-9.4.38.v20210224.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/jetty-server-9.4.38.v20210224.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/jetty-security-9.4.38.v20210224.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/jetty-io-9.4.38.v20210224.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/jetty-http-9.4.38.v20210224.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/javax.servlet-api-3.1.0.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/jackson-databind-2.10.5.1.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/jackson-core-2.10.5.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/jackson-annotations-2.10.5.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/commons-cli-1.4.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../lib/audience-annotations-0.12.0.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../zookeeper-*.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../zookeeper-server/src/main/resources/lib/*.jar:/opt/apache-zookeeper-3.7.0-bin/bin/../conf:
java.library.path=/usr/java/packages/lib/amd64:/usr/lib64:/lib64:/lib:/usr/lib
java.io.tmpdir=/tmp
java.compiler=<NA>
os.name=Linux
os.arch=amd64
os.version=3.10.0-1160.31.1.el7.x86_64
user.name=root
user.home=/root
user.dir=/opt/apache-zookeeper-3.7.0-bin/bin
os.memory.free=55MB
os.memory.max=889MB
os.memory.total=72MB

```

## ruok命令

测试服务是否处于正确运行状态，如果目标正确运行会返回imok（are you ok | I'm ok）

```
echo ruok | nc 192.168.18.128  2181
# 输出
imok

```

## stat命令

输出服务器的详细信息与srvr相似(srvr这里不举例了，官网有一点描述)，但是多了每个连接的会话信息

| 属性 | 含义 |
| - | - |
| zookeeper version | 版本 |
| Latency min/avg/max | 延时 |
| Received | 收包 |
| Sent | 发包 |
| Connections | 当前服务器连接数 |
| Outstanding | 服务器堆积的未处理请求数 |
| Zxid | 最大事务<br>id |
| Mode | 服务器角色 |
| Node count | 节点数 |


```
echo stat | nc 192.168.18.128  2181
# 输出
Zookeeper version: 3.7.0-e3704b390a6697bfdf4b0bef79e3da7a4f6bac4b, built on 2021-03-17 09:46 UTC
Clients:
 /127.0.0.1:55328[1](queued=0,recved=37,sent=37)
 /192.168.18.148:52590[0](queued=0,recved=1,sent=0)

Latency min/avg/max: 0/0.6667/14
Received: 63
Sent: 64
Connections: 2
Outstanding: 0
Zxid: 0x600000001
Mode: follower
Node count: 10

```

## srst命令

重置server状态

```
echo srst | nc 192.168.18.128  2181
# 输出
Server stats reset.

```

## wchc 命令

通过session分组，列出watch的所有节点，它的输出是一个与watch相关的会话的节点列表

```
echo wchc | nc 192.168.18.128  2181

```

## wchp命令

通过路径分组，列出所有的watch的session id 信息

配置同wchc

```
echo wchp | nc 192.168.18.128  2181

```

## mntr命令

列出服务器的健康状态

| 属性 | 含义 |
| - | - |
| zk\_version | 版本 |
| zk\_avg\_latency | 平均延时 |
| zk\_max\_latency | 最大延时 |
| zk\_min\_latency | 最小延时 |
| zk\_packets\_received | 收包数 |
| zk\_packets\_sent | 发包数 |
| zk\_num\_alive\_connections | 连接数 |
| zk\_outstanding\_requests | 堆积请求数 |
| zk\_server\_state | leader/follower<br>状态 |
| zk\_znode\_count | znode<br>数量 |
| zk\_watch\_count | watch<br>数量 |
| zk\_ephemerals\_count | l临时节点<br>(znode) |
| zk\_approximate\_data\_size | 数据大小 |
| zk\_open\_file\_descriptor\_count | 打开的文件描述符数量 |
| zk\_max\_file\_descriptor\_count | 最大文件描述符数量 |


```
echo mntr | nc 192.168.18.128  2181
# 输出
zk_version      3.7.0-e3704b390a6697bfdf4b0bef79e3da7a4f6bac4b, built on 2021-03-17 09:46 UTC
zk_server_state follower
zk_peer_state   following - broadcast
zk_ephemerals_count     0
zk_num_alive_connections        2
zk_avg_latency  0.2778
zk_outstanding_requests 0
zk_znode_count  10
zk_global_sessions      1
zk_non_mtls_remote_conn_count   0
zk_last_client_response_size    16
zk_packets_sent 21
zk_packets_received     21
zk_max_client_response_size     16
zk_connection_drop_probability  0.0
zk_watch_count  0
zk_auth_failed_count    0
zk_min_latency  0
zk_max_file_descriptor_count    4096
zk_approximate_data_size        269
zk_open_file_descriptor_count   75
zk_local_sessions       0
zk_uptime       1473697
zk_max_latency  1
zk_outstanding_tls_handshake    0
zk_min_client_response_size     16
zk_non_mtls_local_conn_count    0
zk_quorum_size  3
zk_proposal_count       0
zk_watch_bytes  0
zk_outstanding_changes_removed  0
zk_throttled_ops        0
zk_stale_requests_dropped       0
zk_large_requests_rejected      0
zk_insecure_admin_count 0
zk_connection_rejected  0
zk_cnxn_closed_without_zk_server_running        0
zk_sessionless_connections_expired      0
zk_looking_count        0
zk_dead_watchers_queued 0
zk_stale_requests       0
zk_connection_drop_count        0
zk_learner_proposal_received_count      0
zk_digest_mismatches_count      0
zk_dead_watchers_cleared        0
zk_response_packet_cache_hits   0
zk_bytes_received_count 228
zk_add_dead_watcher_stall_time  0
zk_request_throttle_wait_count  0
zk_requests_not_forwarded_to_commit_processor   0
zk_response_packet_cache_misses 0
zk_ensemble_auth_success        0
zk_prep_processor_request_queued        0
zk_learner_commit_received_count        0
zk_stale_replies        0
zk_connection_request_count     0
zk_response_bytes       0
zk_ensemble_auth_fail   0
zk_diff_count   0
zk_response_packet_get_children_cache_misses    0
zk_connection_revalidate_count  0
zk_quit_leading_due_to_disloyal_voter   0
zk_snap_count   0
zk_unrecoverable_error_count    0
zk_unsuccessful_handshake       0
zk_commit_count 0
zk_stale_sessions_expired       0
zk_response_packet_get_children_cache_hits      0
zk_sync_processor_request_queued        0
zk_outstanding_changes_queued   0
zk_request_commit_queued        0
zk_ensemble_auth_skip   0
zk_skip_learner_request_to_next_processor_count 0
zk_tls_handshake_exceeded       0
zk_revalidate_count     0
zk_avg_socket_closing_time      0.0
zk_min_socket_closing_time      0
zk_max_socket_closing_time      0
zk_cnt_socket_closing_time      0
zk_sum_socket_closing_time      0
zk_avg_proposal_process_time    0.0
zk_min_proposal_process_time    0
zk_max_proposal_process_time    0
zk_cnt_proposal_process_time    0
zk_sum_proposal_process_time    0
zk_avg_leader_unavailable_time  0.0
zk_min_leader_unavailable_time  0
zk_max_leader_unavailable_time  0
zk_cnt_leader_unavailable_time  0
zk_sum_leader_unavailable_time  0
zk_avg_node_created_watch_count 0.0
zk_min_node_created_watch_count 0
zk_max_node_created_watch_count 0
zk_cnt_node_created_watch_count 0
zk_sum_node_created_watch_count 0
zk_avg_session_queues_drained   0.0
zk_min_session_queues_drained   0
zk_max_session_queues_drained   0
zk_cnt_session_queues_drained   0
zk_sum_session_queues_drained   0
zk_avg_write_commit_proc_req_queued     0.0
zk_min_write_commit_proc_req_queued     0
zk_max_write_commit_proc_req_queued     0
zk_cnt_write_commit_proc_req_queued     18
zk_sum_write_commit_proc_req_queued     0
zk_avg_connection_token_deficit 0.0
zk_min_connection_token_deficit 0
zk_max_connection_token_deficit 0
zk_cnt_connection_token_deficit 0
zk_sum_connection_token_deficit 0
zk_avg_read_commit_proc_req_queued      1.0
zk_min_read_commit_proc_req_queued      1
zk_max_read_commit_proc_req_queued      1
zk_cnt_read_commit_proc_req_queued      18
zk_sum_read_commit_proc_req_queued      18
zk_avg_node_deleted_watch_count 0.0
zk_min_node_deleted_watch_count 0
zk_max_node_deleted_watch_count 0
zk_cnt_node_deleted_watch_count 0
zk_sum_node_deleted_watch_count 0
zk_avg_startup_txns_load_time   0.0
zk_min_startup_txns_load_time   0
zk_max_startup_txns_load_time   0
zk_cnt_startup_txns_load_time   0
zk_sum_startup_txns_load_time   0
zk_avg_sync_processor_queue_size        0.0
zk_min_sync_processor_queue_size        0
zk_max_sync_processor_queue_size        0
zk_cnt_sync_processor_queue_size        0
zk_sum_sync_processor_queue_size        0
zk_avg_follower_sync_time       0.0
zk_min_follower_sync_time       0
zk_max_follower_sync_time       0
zk_cnt_follower_sync_time       0
zk_sum_follower_sync_time       0
zk_avg_prep_processor_queue_size        0.0
zk_min_prep_processor_queue_size        0
zk_max_prep_processor_queue_size        0
zk_cnt_prep_processor_queue_size        0
zk_sum_prep_processor_queue_size        0
zk_avg_fsynctime        0.0
zk_min_fsynctime        0
zk_max_fsynctime        0
zk_cnt_fsynctime        0
zk_sum_fsynctime        0
zk_avg_inflight_snap_count      0.0
zk_min_inflight_snap_count      0
zk_max_inflight_snap_count      0
zk_cnt_inflight_snap_count      0
zk_sum_inflight_snap_count      0
zk_avg_reads_issued_from_session_queue  0.0
zk_min_reads_issued_from_session_queue  0
zk_max_reads_issued_from_session_queue  0
zk_cnt_reads_issued_from_session_queue  0
zk_sum_reads_issued_from_session_queue  0
zk_avg_learner_request_processor_queue_size     0.0
zk_min_learner_request_processor_queue_size     0
zk_max_learner_request_processor_queue_size     0
zk_cnt_learner_request_processor_queue_size     18
zk_sum_learner_request_processor_queue_size     0
zk_avg_snapshottime     0.0
zk_min_snapshottime     0
zk_max_snapshottime     0
zk_cnt_snapshottime     0
zk_sum_snapshottime     0
zk_avg_unavailable_time 0.0
zk_min_unavailable_time 0
zk_max_unavailable_time 0
zk_cnt_unavailable_time 0
zk_sum_unavailable_time 0
zk_avg_startup_txns_loaded      0.0
zk_min_startup_txns_loaded      0
zk_max_startup_txns_loaded      0
zk_cnt_startup_txns_loaded      0
zk_sum_startup_txns_loaded      0
zk_avg_reads_after_write_in_session_queue       0.0
zk_min_reads_after_write_in_session_queue       0
zk_max_reads_after_write_in_session_queue       0
zk_cnt_reads_after_write_in_session_queue       0
zk_sum_reads_after_write_in_session_queue       0
zk_avg_requests_in_session_queue        0.0
zk_min_requests_in_session_queue        0
zk_max_requests_in_session_queue        0
zk_cnt_requests_in_session_queue        0
zk_sum_requests_in_session_queue        0
zk_avg_write_commit_proc_issued 0.0
zk_min_write_commit_proc_issued 0
zk_max_write_commit_proc_issued 0
zk_cnt_write_commit_proc_issued 0
zk_sum_write_commit_proc_issued 0
zk_avg_prep_process_time        0.0
zk_min_prep_process_time        0
zk_max_prep_process_time        0
zk_cnt_prep_process_time        0
zk_sum_prep_process_time        0
zk_avg_pending_session_queue_size       0.0
zk_min_pending_session_queue_size       0
zk_max_pending_session_queue_size       0
zk_cnt_pending_session_queue_size       0
zk_sum_pending_session_queue_size       0
zk_avg_time_waiting_empty_pool_in_commit_processor_read_ms      0.0
zk_min_time_waiting_empty_pool_in_commit_processor_read_ms      0
zk_max_time_waiting_empty_pool_in_commit_processor_read_ms      0
zk_cnt_time_waiting_empty_pool_in_commit_processor_read_ms      0
zk_sum_time_waiting_empty_pool_in_commit_processor_read_ms      0
zk_avg_commit_process_time      0.0556
zk_min_commit_process_time      0
zk_max_commit_process_time      1
zk_cnt_commit_process_time      18
zk_sum_commit_process_time      1
zk_avg_dbinittime       0.0
zk_min_dbinittime       0
zk_max_dbinittime       0
zk_cnt_dbinittime       0
zk_sum_dbinittime       0
zk_avg_inflight_diff_count      0.0
zk_min_inflight_diff_count      0
zk_max_inflight_diff_count      0
zk_cnt_inflight_diff_count      0
zk_sum_inflight_diff_count      0
zk_avg_netty_queued_buffer_capacity     0.0
zk_min_netty_queued_buffer_capacity     0
zk_max_netty_queued_buffer_capacity     0
zk_cnt_netty_queued_buffer_capacity     0
zk_sum_netty_queued_buffer_capacity     0
zk_avg_election_time    0.0
zk_min_election_time    0
zk_max_election_time    0
zk_cnt_election_time    0
zk_sum_election_time    0
zk_avg_commit_commit_proc_req_queued    0.0
zk_min_commit_commit_proc_req_queued    0
zk_max_commit_commit_proc_req_queued    0
zk_cnt_commit_commit_proc_req_queued    18
zk_sum_commit_commit_proc_req_queued    0
zk_avg_sync_processor_batch_size        0.0
zk_min_sync_processor_batch_size        0
zk_max_sync_processor_batch_size        0
zk_cnt_sync_processor_batch_size        0
zk_sum_sync_processor_batch_size        0
zk_avg_node_children_watch_count        0.0
zk_min_node_children_watch_count        0
zk_max_node_children_watch_count        0
zk_cnt_node_children_watch_count        0
zk_sum_node_children_watch_count        0
zk_avg_write_batch_time_in_commit_processor     0.0
zk_min_write_batch_time_in_commit_processor     0
zk_max_write_batch_time_in_commit_processor     0
zk_cnt_write_batch_time_in_commit_processor     0
zk_sum_write_batch_time_in_commit_processor     0
zk_avg_read_commit_proc_issued  1.0
zk_min_read_commit_proc_issued  1
zk_max_read_commit_proc_issued  1
zk_cnt_read_commit_proc_issued  18
zk_sum_read_commit_proc_issued  18
zk_avg_concurrent_request_processing_in_commit_processor        0.0
zk_min_concurrent_request_processing_in_commit_processor        0
zk_max_concurrent_request_processing_in_commit_processor        0
zk_cnt_concurrent_request_processing_in_commit_processor        0
zk_sum_concurrent_request_processing_in_commit_processor        0
zk_avg_node_changed_watch_count 0.0
zk_min_node_changed_watch_count 0
zk_max_node_changed_watch_count 0
zk_cnt_node_changed_watch_count 0
zk_sum_node_changed_watch_count 0
zk_avg_sync_process_time        0.0
zk_min_sync_process_time        0
zk_max_sync_process_time        0
zk_cnt_sync_process_time        0
zk_sum_sync_process_time        0
zk_avg_startup_snap_load_time   0.0
zk_min_startup_snap_load_time   0
zk_max_startup_snap_load_time   0
zk_cnt_startup_snap_load_time   0
zk_sum_startup_snap_load_time   0
zk_avg_prep_processor_queue_time_ms     0.0
zk_min_prep_processor_queue_time_ms     0
zk_max_prep_processor_queue_time_ms     0
zk_cnt_prep_processor_queue_time_ms     0
zk_sum_prep_processor_queue_time_ms     0
zk_p50_prep_processor_queue_time_ms     0
zk_p95_prep_processor_queue_time_ms     0
zk_p99_prep_processor_queue_time_ms     0
zk_p999_prep_processor_queue_time_ms    0
zk_avg_jvm_pause_time_ms        0.0
zk_min_jvm_pause_time_ms        0
zk_max_jvm_pause_time_ms        0
zk_cnt_jvm_pause_time_ms        0
zk_sum_jvm_pause_time_ms        0
zk_p50_jvm_pause_time_ms        0
zk_p95_jvm_pause_time_ms        0
zk_p99_jvm_pause_time_ms        0
zk_p999_jvm_pause_time_ms       0
zk_avg_close_session_prep_time  0.0
zk_min_close_session_prep_time  0
zk_max_close_session_prep_time  0
zk_cnt_close_session_prep_time  0
zk_sum_close_session_prep_time  0
zk_p50_close_session_prep_time  0
zk_p95_close_session_prep_time  0
zk_p99_close_session_prep_time  0
zk_p999_close_session_prep_time 0
zk_avg_read_commitproc_time_ms  0.1111
zk_min_read_commitproc_time_ms  0
zk_max_read_commitproc_time_ms  1
zk_cnt_read_commitproc_time_ms  18
zk_sum_read_commitproc_time_ms  2
zk_p50_read_commitproc_time_ms  0
zk_p95_read_commitproc_time_ms  1
zk_p99_read_commitproc_time_ms  1
zk_p999_read_commitproc_time_ms 1
zk_avg_updatelatency    0.0
zk_min_updatelatency    0
zk_max_updatelatency    0
zk_cnt_updatelatency    0
zk_sum_updatelatency    0
zk_p50_updatelatency    0
zk_p95_updatelatency    0
zk_p99_updatelatency    0
zk_p999_updatelatency   0
zk_avg_local_write_committed_time_ms    0.0
zk_min_local_write_committed_time_ms    0
zk_max_local_write_committed_time_ms    0
zk_cnt_local_write_committed_time_ms    0
zk_sum_local_write_committed_time_ms    0
zk_p50_local_write_committed_time_ms    0
zk_p95_local_write_committed_time_ms    0
zk_p99_local_write_committed_time_ms    0
zk_p999_local_write_committed_time_ms   0
zk_avg_request_throttle_queue_time_ms   0.0556
zk_min_request_throttle_queue_time_ms   0
zk_max_request_throttle_queue_time_ms   1
zk_cnt_request_throttle_queue_time_ms   18
zk_sum_request_throttle_queue_time_ms   1
zk_p50_request_throttle_queue_time_ms   0
zk_p95_request_throttle_queue_time_ms   1
zk_p99_request_throttle_queue_time_ms   1
zk_p999_request_throttle_queue_time_ms  1
zk_avg_readlatency      0.2778
zk_min_readlatency      0
zk_max_readlatency      1
zk_cnt_readlatency      18
zk_sum_readlatency      5
zk_p50_readlatency      0
zk_p95_readlatency      1
zk_p99_readlatency      1
zk_p999_readlatency     1
zk_avg_quorum_ack_latency       0.0
zk_min_quorum_ack_latency       0
zk_max_quorum_ack_latency       0
zk_cnt_quorum_ack_latency       0
zk_sum_quorum_ack_latency       0
zk_p50_quorum_ack_latency       0
zk_p95_quorum_ack_latency       0
zk_p99_quorum_ack_latency       0
zk_p999_quorum_ack_latency      0
zk_avg_om_commit_process_time_ms        0.0
zk_min_om_commit_process_time_ms        0
zk_max_om_commit_process_time_ms        0
zk_cnt_om_commit_process_time_ms        0
zk_sum_om_commit_process_time_ms        0
zk_p50_om_commit_process_time_ms        0
zk_p95_om_commit_process_time_ms        0
zk_p99_om_commit_process_time_ms        0
zk_p999_om_commit_process_time_ms       0
zk_avg_read_final_proc_time_ms  0.1111
zk_min_read_final_proc_time_ms  0
zk_max_read_final_proc_time_ms  1
zk_cnt_read_final_proc_time_ms  18
zk_sum_read_final_proc_time_ms  2
zk_p50_read_final_proc_time_ms  0
zk_p95_read_final_proc_time_ms  1
zk_p99_read_final_proc_time_ms  1
zk_p999_read_final_proc_time_ms 1
zk_avg_commit_propagation_latency       0.0
zk_min_commit_propagation_latency       0
zk_max_commit_propagation_latency       0
zk_cnt_commit_propagation_latency       0
zk_sum_commit_propagation_latency       0
zk_p50_commit_propagation_latency       0
zk_p95_commit_propagation_latency       0
zk_p99_commit_propagation_latency       0
zk_p999_commit_propagation_latency      0
zk_avg_dead_watchers_cleaner_latency    0.0
zk_min_dead_watchers_cleaner_latency    0
zk_max_dead_watchers_cleaner_latency    0
zk_cnt_dead_watchers_cleaner_latency    0
zk_sum_dead_watchers_cleaner_latency    0
zk_p50_dead_watchers_cleaner_latency    0
zk_p95_dead_watchers_cleaner_latency    0
zk_p99_dead_watchers_cleaner_latency    0
zk_p999_dead_watchers_cleaner_latency   0
zk_avg_write_final_proc_time_ms 0.0
zk_min_write_final_proc_time_ms 0
zk_max_write_final_proc_time_ms 0
zk_cnt_write_final_proc_time_ms 0
zk_sum_write_final_proc_time_ms 0
zk_p50_write_final_proc_time_ms 0
zk_p95_write_final_proc_time_ms 0
zk_p99_write_final_proc_time_ms 0
zk_p999_write_final_proc_time_ms        0
zk_avg_proposal_ack_creation_latency    0.0
zk_min_proposal_ack_creation_latency    0
zk_max_proposal_ack_creation_latency    0
zk_cnt_proposal_ack_creation_latency    0
zk_sum_proposal_ack_creation_latency    0
zk_p50_proposal_ack_creation_latency    0
zk_p95_proposal_ack_creation_latency    0
zk_p99_proposal_ack_creation_latency    0
zk_p999_proposal_ack_creation_latency   0
zk_avg_proposal_latency 0.0
zk_min_proposal_latency 0
zk_max_proposal_latency 0
zk_cnt_proposal_latency 0
zk_sum_proposal_latency 0
zk_p50_proposal_latency 0
zk_p95_proposal_latency 0
zk_p99_proposal_latency 0
zk_p999_proposal_latency        0
zk_avg_om_proposal_process_time_ms      0.0
zk_min_om_proposal_process_time_ms      0
zk_max_om_proposal_process_time_ms      0
zk_cnt_om_proposal_process_time_ms      0
zk_sum_om_proposal_process_time_ms      0
zk_p50_om_proposal_process_time_ms      0
zk_p95_om_proposal_process_time_ms      0
zk_p99_om_proposal_process_time_ms      0
zk_p999_om_proposal_process_time_ms     0
zk_avg_sync_processor_queue_and_flush_time_ms   0.0
zk_min_sync_processor_queue_and_flush_time_ms   0
zk_max_sync_processor_queue_and_flush_time_ms   0
zk_cnt_sync_processor_queue_and_flush_time_ms   0
zk_sum_sync_processor_queue_and_flush_time_ms   0
zk_p50_sync_processor_queue_and_flush_time_ms   0
zk_p95_sync_processor_queue_and_flush_time_ms   0
zk_p99_sync_processor_queue_and_flush_time_ms   0
zk_p999_sync_processor_queue_and_flush_time_ms  0
zk_avg_propagation_latency      0.0
zk_min_propagation_latency      0
zk_max_propagation_latency      0
zk_cnt_propagation_latency      0
zk_sum_propagation_latency      0
zk_p50_propagation_latency      0
zk_p95_propagation_latency      0
zk_p99_propagation_latency      0
zk_p999_propagation_latency     0
zk_avg_server_write_committed_time_ms   0.0
zk_min_server_write_committed_time_ms   0
zk_max_server_write_committed_time_ms   0
zk_cnt_server_write_committed_time_ms   0
zk_sum_server_write_committed_time_ms   0
zk_p50_server_write_committed_time_ms   0
zk_p95_server_write_committed_time_ms   0
zk_p99_server_write_committed_time_ms   0
zk_p999_server_write_committed_time_ms  0
zk_avg_sync_processor_queue_time_ms     0.0
zk_min_sync_processor_queue_time_ms     0
zk_max_sync_processor_queue_time_ms     0
zk_cnt_sync_processor_queue_time_ms     0
zk_sum_sync_processor_queue_time_ms     0
zk_p50_sync_processor_queue_time_ms     0
zk_p95_sync_processor_queue_time_ms     0
zk_p99_sync_processor_queue_time_ms     0
zk_p999_sync_processor_queue_time_ms    0
zk_avg_sync_processor_queue_flush_time_ms       0.0
zk_min_sync_processor_queue_flush_time_ms       0
zk_max_sync_processor_queue_flush_time_ms       0
zk_cnt_sync_processor_queue_flush_time_ms       0
zk_sum_sync_processor_queue_flush_time_ms       0
zk_p50_sync_processor_queue_flush_time_ms       0
zk_p95_sync_processor_queue_flush_time_ms       0
zk_p99_sync_processor_queue_flush_time_ms       0
zk_p999_sync_processor_queue_flush_time_ms      0
zk_avg_write_commitproc_time_ms 0.0
zk_min_write_commitproc_time_ms 0
zk_max_write_commitproc_time_ms 0
zk_cnt_write_commitproc_time_ms 0
zk_sum_write_commitproc_time_ms 0
zk_p50_write_commitproc_time_ms 0
zk_p95_write_commitproc_time_ms 0
zk_p99_write_commitproc_time_ms 0
zk_p999_write_commitproc_time_ms        0
zk_avg_zookeeper_write_per_namespace    0.0
zk_min_zookeeper_write_per_namespace    0
zk_max_zookeeper_write_per_namespace    0
zk_cnt_zookeeper_write_per_namespace    0
zk_sum_zookeeper_write_per_namespace    0
zk_avg_root_write_per_namespace 0.0
zk_min_root_write_per_namespace 0
zk_max_root_write_per_namespace 0
zk_cnt_root_write_per_namespace 0
zk_sum_root_write_per_namespace 0
zk_avg_event_write_per_namespace        0.0
zk_min_event_write_per_namespace        0
zk_max_event_write_per_namespace        0
zk_cnt_event_write_per_namespace        0
zk_sum_event_write_per_namespace        0
zk_avg_home_write_per_namespace 0.0
zk_min_home_write_per_namespace 0
zk_max_home_write_per_namespace 0
zk_cnt_home_write_per_namespace 0
zk_sum_home_write_per_namespace 0

```

        