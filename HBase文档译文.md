# Hbase文档译文

翻译文件来自 : Hbase官方文档

# 入门

### 1.快速入门-单节点独立运行的Hbase

本章节将介绍,一个包含所有进程的,单节点 , 独立运行的hbase ,他包含了,Master节点,RegionServers节点,Zookeeper,在一个持久存在的本地文件系统的jvm中运行,这里是hbase最基本的配置文件部署,将展示如何在Hbase的shell客户端中创建表,将行插入表,对表执行放置和扫描操作,禁用或启用Hbase

整个操作不会超过10分钟,当然,需要排除你下载Hbase的时间

### 2. 所需要的JDK版本

Hbase需要安装JDK,有关支持的JDK版本信息请参阅JAVA

### 3. Get Started with Hbase   Hbase入门

步骤:请查看独立模式下载,配置和启动Hbase

1. 请从以下列表中选择下载Apache Download Mirrors,单击建议的顶部链接,会将你带入Hbase的镜像中,点击名为stable的文件夹,然后将以.tar.gz结尾的二进制文件下载到你的本地系统,暂时不要下载以src.tar.gz结尾的文件.

1. 解压你所下载的文件,并且更改为新创建的目录

```
$ tar xzvf hbase-3.0.0-alpha-4-SNAPSHOT-bin.tar.gz
$ cd hbase-3.0.0-alpha-4-SNAPSHOT/
```

1. 启动Hbase之前,你必须先将JAVA_HOME写入环境变量中,为了可以更方便的调用 conf/hbase-env.conf 文件,配置jdk之后编辑conf/hbase-env.sh文件并取消注释以#export JAVA_HOME=开头的行，然后将其设置为JAVA安装路径。

```
# Example extract from hbase-env.sh where JAVA_HOME is set
# Set environment variables here.
# The java implementation to use.
export JAVA_HOME=/usr/jdk64/jdk1.8.0_112
```

1. bin/start-hbase.sh 脚本是提供hbase的便捷启动方式之一,运行命令,如果一切顺利的话,控制台会打印显示Hbase已启动,你可以通过JPS 检查是否有一个名为HMaster的进程,在独立模式下,Hbase在单个JVM中运行所有的守护进程,包含HMaster、单HRegionServer和ZooKeeper守护进程,访问转到http://localhost:16010以查看HBase Web UI。

程序! 首次使用Hbase

1. 链接到Hbase

使用位于HBase安装的bin/目录中的HBase-shell命令连接到正在运行的HBase实例。在本例中，省略了启动HBaseShell时打印的一些用法和版本信息。HBase Shell提示以>字符结束。

```
$ ./bin/hbase shell
hbase(main):001:0>
```

1. 显示Hbase shell帮助文本

键入help 并且回车,显示基本的语法信息帮助,请注意 ,所有的表名,行,列,都必须引号包裹

1. 创建表

使用create命令可以创建一个新表。必须指定表名称和ColumnFamily名称。

```
hbase(main):001:0> create 'test', 'cf'
0 row(s) in 0.4170 seconds
=> Hbase::Table - test
```

1. 列出表信息

使用 list 命令确认表存在

```
hbase(main):002:0> list 'test'
TABLE
test
1 row(s) in 0.0180 seconds
=> ["test"]
```

1. 现在使用describe命令查看详细信息，包括配置默认值

```
hbase(main):003:0> describe 'test'
Table test is ENABLED
test
COLUMN FAMILIES DESCRIPTION
{NAME => 'cf', VERSIONS => '1', EVICT_BLOCKS_ON_CLOSE => 'false', NEW_VERSION_BEHAVIOR => 'false', KEEP_DELETED_CELLS => 'FALSE', CACHE_DATA_ON_WRITE =>
'false', DATA_BLOCK_ENCODING => 'NONE', TTL => 'FOREVER', MIN_VERSIONS => '0', REPLICATION_SCOPE => '0', BLOOMFILTER => 'ROW', CACHE_INDEX_ON_WRITE => 'f
alse', IN_MEMORY => 'false', CACHE_BLOOMS_ON_WRITE => 'false', PREFETCH_BLOCKS_ON_OPEN => 'false', COMPRESSION => 'NONE', BLOCKCACHE => 'true', BLOCKSIZE
 => '65536'}
1 row(s)
Took 0.9998 seconds
```

1. 存放数据到你的表格中

如果想存放数据到你的表格中,可以使用put关键词

```
hbase(main):003:0> put 'test', 'row1', 'cf:a', 'value1'
0 row(s) in 0.0850 seconds

hbase(main):004:0> put 'test', 'row2', 'cf:b', 'value2'
0 row(s) in 0.0110 seconds

hbase(main):005:0> put 'test', 'row3', 'cf:c', 'value3'
0 row(s) in 0.0100 seconds
```

在这里,我们插入了三条数据,一次一个,第一条数据插入位于第一行的cf:a列,值为value1 ,hbase的列由列族前缀(本例为cf),冒号,和列限定符后缀,笨(本例为a)组成

1. 