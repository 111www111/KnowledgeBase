# Hbase

关于自身学习Hbase的感悟以及总结

## 1. 前提

依赖框架：

1. Haoop

1. Zookeeper

整合：

1. hive

1. Phoenix

本质上，Hbase是一个以HDFS为基础的，一种分布式扩展的Nosql数据库，在开始的地方我们需要学习一下简单的概念，例如Hbase的数据模型，以及结构和架构，方便我们上手

### 1.1 Hbase 的数据模型

这里建议看一下Google的BigTable的论文，对于论文中的总结，大数据的数据表设计，是一种稀疏的，分布式，持久的map，之后对于映射的解释就是：

```
该映射由，行键，列键，和时间戳索引，映射中每个值都是一个未解释的字节数组
```

最终，Hbase 关于数据模型和BigTable的对应总结来说就是：

```
Hbase 使用与BigTable 相似的数据模型，用户将数据行存储在带标签的表格中，
数据行具有可排序的键和任意数量的列。该表存储系数，也可以在同一表中的行可以有自定义程度很高的列
```

所以，最终理解Hbase的数据模型的关键词为：系数，分布式，多维度，排序的映射的KV结构

### 1.2 Hbase的逻辑结构

Hbase可以用于存储多种结构的数据，比如复杂且有序的json，讲道理，这里有点像es

![](images/WEBRESOURCE92dbf51cc8644aaf6cf9a05d83097d22.octet-stream)

我们来看下，这种数据在Hbase的映射是什么样子的，这里 我还是直接搬运尚硅谷方便点，简单来说就是横向拆一拆，纵向拆一拆，自带分库分表

![](images/WEBRESOURCE428c3c44a890f633f60e560317ec558d.octet-stream)

那么依照这个逻辑，在Hbase的底层应该是怎么保存数据的呢？

首先我们需要知道，Hbase的数据存储是基于Hdfs的，而Hdfs最大的特点就是，只能追加数据，不能修改，而对于Hbase 而言，数据库不能修改是硬伤，而Hbase在不能改数据的基础上实现了可以修改的需求，总结而言就是，根据版本号，时间戳来区分最新的数据，还有删除标记type，这种根据时间戳来操作的数据，我们称之为”拉链表“

![](images/WEBRESOURCEe5a19f62daaa8fb0306eff7c8d4cd455.octet-stream)

### 1.3  Hbase 的数据模型

1. Name Space

命名空间，类似于关系型数据库的database的概念，每个命名空间下面有多个表，Hbase自带两个命名空间，分别是hbase 和 default ，hbase中存放的是Hbase的内置表，default是用户默认使用的命名空间

1. Table

类似于关系型数据库表的概念，不同的是，Hbase定义表时只需要声名列族，不需要声明具体的列，因为数据存储是稀疏的，所有向Hbase写入数据时，字段可以动态的自定义，因此 Hbase能更轻松的应对字段变更的场景。

1. Row

Hbase表中每行数据都由一个RowKey 和 多个 Column(列)组成，数据按照RowKey的字典顺序存储的，并且查询数据只能根据RowKey查询，所以 RowKey 的设计十分重要

1. Column

Hbase的每列都是由 Column Family （列族） 和 Column Qualifier（列限定符。也可以叫列名）进行限定，例如

info：name，info：age，建表时只需要写列族即可，列名不需要预先定义，可以自定义增加和删减

1. Time Stamp

用于标识数据的不同版本（version），每条数据写入时，系统为自动加上该字段，并写入Hbase的时间

1. Cell

由{rowkey，column Family：column Qualifier，timestamp } 唯一确定的单元，cell中数据全部是字节码

## 2. 快速上手

### 2.1 安装部署

1. zookeeper  hadoop搭建，略

1. 解压Hbase

1. 配置环境变量

```
export HBASE_HOME=/software/hbase/hbase
export PATH=$PATH:$HBASE_HOME/bin
//生效
source /etc/profile
```

1. 配置配置文件

hbase-env.sh

```
# 关闭 Hbase 自带的zookeeper 
export HBASE_MANAGES_ZK=flase
```

hbase-site.xml

```
<configuration>
  <!-- 是否部署为集群 -->
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <!-- zookeeper -->
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>hadoop101,hadoop102,hadoop103</value>
  </property>
  <!-- zookeeper 持久化 -->
  <property>
    <name>hbase.zookeeper.property.dataDir</name>
    <value>/software/hbase/hbase/zokeperData</value>
  </property>
  <!-- Hbase 放在HDFS上面 -->
  <property>
    <name>hbase.rootdit</name>
    <value>hdfs://hadoop102:8020/hbase</value>
  </property>
</configuration>
```

1. 配置regionservers

```
hadoop101
hadoop102
hadoop103
```

1. 雷区： 解决Hbase 与 hadoop 的Log4j的兼容问题，把Hbase的jar删了 ，然后把hadoop的copy过来就OK

```
[hadoop@hadoop101 client-facing-thirdparty]$ mv slf4j-reload4j-1.7.33.jar slf4j-reload4j-1.7.33.jar.bak
```

1. 单点启动

```
hbase-daemon.sh start master
hbase-daemon.sh start regionserver
```

1. 群启

```
start-hbase.sh
```

1. 停止对应服务

```
stop-hbase.sh
```

![](images/WEBRESOURCE2e98b102930de0f30cc7461054badaa7.octet-stream)

这就是启动了，我们可以去UI界面查看 hadoop101:16010

![](images/WEBRESOURCE67ed54e86b122ce68a88e75a6ffbd2ee.octet-stream)

1. 高可用 划重点

1. 先关闭集群

```
stop-hbase.sh
```

1. 在conf目录下创建backup-masters文件

```
touch conf/backup-masters
```

1. 配置高可用节点

```
echo hadoop103 > conf/backup-masters
```

1. 分发重启,打开页面查看

### 2.2 操作

#### 2.2.1 Hbase shell操作

1. 基本操作

1. 进入Hbase客户端命令行

```
[hadoop@hadoop101] hbase shell
Use "help" to get list of supported commands.
Use "exit" to quit this interactive shell.
For Reference, please visit: http://hbase.apache.org/2.0/book.html#shell
Version 2.4.17, r7fd096f39b4284da9a71da3ce67c48d259ffa79a, Fri Mar 31 18:10:45 UTC 2023
Took 0.0054 seconds                                                                                            
hbase:001:0> 
```

1. 查看帮助命令

```
-- 能够展示Hbase 中所有能使用的命令，主要使用的命令有namespace相关，DDL创建修改表格，DML写入读取数据
hbase:001:0>  help
```

1. namespace	

1. 展示系统中存在的命名空间

```
list_namespace
```

1. 创建命名空间

```
create_namespace 'ns1'
```

1. DDL

1. 查看表格

```
list
```

1. 创建表格

```
创建    表名      列族名1 2
create 'student','info','msg'

create 'bigdata:person' ,{NAME=>'info',VERSIONS=>5},{NAME=> 'msg',VERSIONS=>5}
VERSION 关键词，维护五个版本
```

怎么会有这么SB的语法

![](images/WEBRESOURCEa9f40dfe0d6320b142fd80710bf9de9b.octet-stream)

1. 详细信息

```
describe 'table_name'
```

1. 修改表

```
alter 'table_name',{NAME=>'F1',VERSIONS=>3}
```

1. 删除 高危操作

```
-- 先标记不可用
disable 'table_name'
-- 删除
drop 'table_name'
```

1. DML

1. 写入数据

在Hbase中如果像写入数据，只能添加结构最底层的cell，可以手动写入时间戳指定cell版本，推荐不写默认使用当前的系统时间

```
put 'bigdata:student','1001','info:name','zhangsan'
put 'bigdata:student','1001','info:name','lisi'
put 'bigdata:student','1001','info:age','18'
```

如果重复写入相同的rowKey，相同列的数据会写入多个版本进行覆盖

1. 读取数据

Hbase中 读取数据有两个方法，get 和 scan

get最大范围时一行数据，也可以进行列的过滤，读取的结果为多行cell

```
get 'bigdata:student','1001'
get 'bigdata:student','1001',{COLUMN=>'info:name'}
```

也可以修改读取cell的版本数，默认读取一个，最多能够读取当前列族设置的维护版本号

```
get 'bigdata:student','1001',{COLUMN=>'info:name',VERSIONS => 6}
```

scan 读取多行数据 

```
scan 'table_name'
-- 扫描多行，左闭右开
scan 'ns1:t1', {COLUMNS => ['c1', 'c2'], LIMIT => 10, STARTROW => 'xyz'}
scan 't1', {COLUMNS => ['c1', 'c2'], LIMIT => 10, STARTROW => 'xyz'}
```

1. 删除数据

删除数据的方法有两个：delete 和 deleteall

```
-- delete 表示删除一个版本的数据，即一个cell，不填写版本默认删除最新的
-- deleteall 表示删除所有版本的数据
delete 'bigdata:student','1001','info:name'
deleteall 'bigdata:student','1001','info:name'
```

![](images/WEBRESOURCEeaaeab90e2a04aa8b0ef66e1f3626d85.octet-stream)

![](images/WEBRESOURCEb015f2cc04328807cf43dbf8bdf9dad5.octet-stream)

怎么会有这么傻逼的语法？

#### 2.2.2 Hbase API操作

新建项目，然后再pom.XML 中添加依赖

注意：如果报错javax.el不存在，解锁注释部分

```
    <dependencies>
        <!--hbase-->
        <dependency>
            <groupId>org.apache.hbase</groupId>
            <artifactId>hbase-client</artifactId>
            <version>2.4.17</version>
<!--            <exclusions>-->
<!--                <exclusion>-->
<!--                    <groupId>org.glassfish</groupId>-->
<!--                    <artifactId>javax.el</artifactId>-->
<!--                </exclusion>-->
<!--            </exclusions>-->
        </dependency>
<!--        <dependency>-->
<!--            <groupId>org.glassfish</groupId>-->
<!--            <artifactId>javax.el</artifactId>-->
<!--            <version>3.0.1-b06</version>-->
<!--        </dependency>-->
    </dependencies>
```

 2.2.2.1 DDL

Hbase的客户端由ConnectionFactory的工厂创建，需要手动关闭链接，推荐一个进程使用一个链接，对Hbase的命令通过链接中的两个属性 Admin 和Table 来实现

单线程创建链接

```
/**
 * 创建链接
 * @param args
 */
public static void main(String[] args){
    Connection connection = null;
    try {
        Configuration configuration = new Configuration();
        configuration.set("hbase.zookeeper.quorum","hadoop101,hadoop102,hadoop103");
        //创建链接 参数 conf
        connection = ConnectionFactory.createConnection(configuration);
        //默认使用同步链接,这里提供了异步链接
        //CompletableFuture<AsyncConnection> asyncConnection = ConnectionFactory.createAsyncConnection(configuration);
        //使用链接
        System.out.println(connection);
    } catch (IOException e) {
        throw new RuntimeException(e);
    }finally {
        if(Objects.nonNull(connection)){
            try {
                connection.close();
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        }
    }
}
```

多线程创建链接，读取hbase-site.xml文件

```
public class HbaseConnection {

    /**
     * 多线程创建链接，类似于单例，推荐这个
     */
    public static Connection connection = null;
    static{
        try {
            //创建链接 参数 conf,读取hbase-site.xml文件
            connection = ConnectionFactory.createConnection();
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
    
    public static void closeConnection(){
        if(Objects.nonNull(connection)){
            try {
                connection.close();
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        }
    }

    public static void main(String[] args){
        System.out.println(connection);
        closeConnection();
    }
}
```

创建nameSpace

```java
/**
 * 创建命名空间
 * admin的链接是轻量级的，并不是线程安全的，不推荐池化，或者缓存
 * @param namespace 命名空间名称
 */
public static void createNamespace(String namespace) throws IOException {
    //获取admin
    Admin admin = connection.getAdmin();
    //调用方法,写入命名空间的描述器
    NamespaceDescriptor.Builder builder = NamespaceDescriptor.create(namespace);
    //添加你像要的键值对，可以给map
    builder.addConfiguration("user","wyt");
    admin.createNamespace(builder.build());
    //关闭链接
    admin.close();
}
```

判断表格是否存在

```
/**
 * 判断表格是否存在
 * @param namespace 命名空间名称
 * @param namespace 表明
 * @return boolean
 */
public static boolean isTableExists(String namespace,String tableName) throws IOException {
    Admin admin = connection.getAdmin();
    TableName table = TableName.valueOf(namespace, tableName);
    return admin.tableExists(table);
}
```

创建表格

```
/**
 * 创建表格
 *
 * @param namespace      命名空间名称
 * @param namespace      表名
 * @param columnFamilies 列族
 * @return boolean
 */
public static void createTable(String namespace,
                               String tableName,
                               String... columnFamilies) throws IOException {
    Admin admin = connection.getAdmin();
    TableDescriptorBuilder tableDescriptorBuilder = TableDescriptorBuilder.newBuilder(TableName.valueOf(namespace, tableName));
    for (String columnFamily:columnFamilies){
        //放列族
        tableDescriptorBuilder.setColumnFamily(
                ColumnFamilyDescriptorBuilder
                        //列族
                        .newBuilder(Bytes.toBytes(columnFamily))
                        //版本参数
                        .setMaxVersions(5)
                        .build()
        );
    }
    admin.createTable(tableDescriptorBuilder.build());
    admin.close();
}
```

修改表格的列族信息

```
/**
 * 更改一个表的列族的版本
 *
 * @param namespace        命名空间
 * @param tableName        表名
 * @param columnFamilyName 列族名
 * @param version          版本
 */
public static void updateColumnFamilyVersion(String namespace,
                                             String tableName,
                                             String columnFamilyName,
                                             int version) throws IOException {
    Admin admin = connection.getAdmin();
    //获取表格信息
    TableDescriptor descriptor = admin.getDescriptor(TableName.valueOf(namespace, tableName));
    TableDescriptorBuilder tableDescriptorBuilder = TableDescriptorBuilder.newBuilder(descriptor);
    //如果你是一个新的参数，那么其他的参数会初始化为默认值，所以要先拿到这个列族
    ColumnFamilyDescriptor columnFamily = descriptor.getColumnFamily(Bytes.toBytes(columnFamilyName));
    tableDescriptorBuilder.modifyColumnFamily(
            ColumnFamilyDescriptorBuilder
                    //列族
                    .newBuilder(columnFamily)
                    //版本参数
                    .setMaxVersions(version)
                    .build()
    );
    admin.modifyTable(tableDescriptorBuilder.build());
    admin.close();
}
```

删除表

```
/**
 * 删除表格
 *
 * @param namespace 命名空间
 * @param tableName 表名
 */
public static void deleteTable(String namespace, String tableName) throws IOException {
    Admin admin = connection.getAdmin();
    //先判断是否存在
    if(isTableExists(namespace, tableName)){
        //先禁用表格
        admin.disableTable(TableName.valueOf(namespace, tableName));
        //删除表格
        admin.deleteTable(TableName.valueOf(namespace, tableName));
    }
    admin.close();
}
```

 2.2.2.2 DML

插入数据

```
/**
 * 插入一条数据
 *
 * @param namespace    命名空间
 * @param tableName    表名
 * @param rowKey       行键
 * @param columnFamily 列族
 * @param column       列
 * @param value        值
 */
public static void insertOne(String namespace, String tableName,
                             String rowKey, String columnFamily, String column,
                             String value) throws IOException {
    //获取table
    Table table = connection.getTable(TableName.valueOf(namespace, tableName));
    //添加rowKey
    Put put = new Put(Bytes.toBytes(rowKey));
    //添加数据
    put.addColumn(Bytes.toBytes(columnFamily), Bytes.toBytes(column), Bytes.toBytes(value));
    table.put(put);
    table.close();
}
```

![](images/WEBRESOURCE87a670ac33829f7b8e57c5057d6e439d.octet-stream)



读取一条数据，根据主键

```
/**
 * 读取一条数据
 * @param namespace
 * @param tableName
 * @param rowKey
 * @param columnFamily
 * @param column
 * @param version
 */
public static void selectByIds(String namespace, String tableName,
                               String rowKey,String columnFamily,String column,int version) throws IOException {
    Table table = connection.getTable(TableName.valueOf(namespace, tableName));
    //get对象
    Get get = new Get(Bytes.toBytes(rowKey));
    if(StringUtils.isNotEmpty(columnFamily) && StringUtils.isNotEmpty(column)){
        get.addColumn(Bytes.toBytes(columnFamily),Bytes.toBytes(column));
    }
    if(version != 0 ){
        get.readVersions(version);
    }
    Result result = table.get(get);
    Cell[] cells = result.rawCells();
    for (Cell cell : cells) {
        String value = new String(CellUtil.cloneValue(cell));
        System.out.println(value);
    }
    table.close();
}
```

扫描数据

```
/**
 * 扫描数据
 * @param namespace
 * @param tableName
 * @param startRow
 * @param endRow
 */
public static void scanRows(String namespace,String tableName,String startRow,String endRow) throws IOException {
    Table table = connection.getTable(TableName.valueOf(namespace, tableName));
    Scan scan = new Scan();
    scan.withStartRow(Bytes.toBytes(startRow));
    scan.withStopRow(Bytes.toBytes(endRow));
    ResultScanner scanner = table.getScanner(scan);
    for (Result result : scanner) {
        Cell[] cells = result.rawCells();
        for (Cell cell : cells) {
            String value = new String(CellUtil.cloneValue(cell));
            System.out.print(value);
        }
        System.out.println();
    }
    table.close();
}
```

列族过滤

```
/**
 * 列族过滤
 * @param namespace
 * @param tableName
 * @param columnFamily
 * @param column
 * @param dataValue
 * @throws IOException
 */
public static void scanRowsByColumnAndValue(String namespace,String tableName,
                                            String columnFamily,String column,
                                            String dataValue) throws IOException {
    Table table = connection.getTable(TableName.valueOf(namespace, tableName));
    //过滤条件 要么只保留符合条件的，要么只排除符合条件的
    FilterList filterList = new FilterList();
    //只保留的 ，这里如果想保留整行数据 请使用 SingleColumnValueFilter 过滤器 但是 会保留没有当前列的数据
    ColumnValueFilter columnValueFilter = new ColumnValueFilter(
            //列族
            Bytes.toBytes(columnFamily),
            //列
            Bytes.toBytes(column),
            //条件
            CompareOperator.EQUAL,
            //值
            Bytes.toBytes(dataValue)
    );
    filterList.addFilter(columnValueFilter);
    //查询条件
    Scan scan = new Scan();
    scan.setFilter(filterList);
    //开始查
    ResultScanner scanner = table.getScanner(scan);
    for (Result result : scanner) {
        Cell[] cells = result.rawCells();
        for (Cell cell : cells) {
            String value = new String(CellUtil.cloneValue(cell));
            System.out.print(value);
        }
        System.out.println();
    }
    table.close();
}
```

删除，一般不会用这个，感觉都是拉链表盖就行了

```
/**
 * 删除数据
 *
 * @param namespace
 * @param tableName
 * @param rowKey
 * @param columnFamily
 * @param column
 */
public static void deleteById(String namespace, String tableName, String rowKey,
                              String columnFamily, String column) throws IOException {
    Table table = connection.getTable(TableName.valueOf(namespace, tableName));
    Delete delete = new Delete(Bytes.toBytes(rowKey));
    //hbase不能直接删除一整行的数据，只能按列删
    //addColumn 删除一个版本
    //addColumns 删除多个版本
    delete.addColumn(Bytes.toBytes(columnFamily), Bytes.toBytes(column));
    table.delete(delete);
    table.close();
}
```

后记，关于Hbase的API 个人更倾向于专人进行封装之后来处理，本质上类似elasticsearch的原生API一样，偏向底层的结果就是写起来异常的麻烦，而且需要new极多的对象，所以建议按照业务需求封装成jar导入

## 3. 企业应用开发

### 3.1 RowKey 设计

rowkey 本质上就是数据的唯一标志，这条数据存储在哪个分区，取决于rowkey处于哪一个预先分区的区间，设计rowkey的主要目的，就是让数据均匀分布与所有的region中，在一定程度上防止数据倾斜，那么看下调研针对需求设计的rowKey

在一般的项目中，数据库的主键生成大致分为下列三种

1. 生成随机数，hash，散列值，雪花等

1. 时间戳等反转

1. UUID，或者是字符串拼接

我们来看下这样的一个需求：

使用Hbase存储下列数据，要求能够通过hbase的API读取数据完成两个统计需求

（1）统计张三在2021年12月消费的总金额

（2）统计所有人在2021年12月分的消费总额

以下是数据文件（样式）

![](images/WEBRESOURCE621b7617ca873aba26abe9ac35b8053d.octet-stream)

我们来分析一下需求，如果是常规的方法，我们可能直接使用这样的语法处理

```
scan：startRow => zhangsan
      stopRow  => zhangsan
```

在实际应用中，这样大概率是避免不了出现zhangsanfeng这样的噪声数据，为了解决这样的问题，官方提供了填充字段的方法，保证所有的数据有相同的长度

例如，填充ASCII码=1的 ^A 使得其长度一致

### 3.2 TSDB

### 3.3 基础表格模式