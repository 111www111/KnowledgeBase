# Mycat1部分(1.6)

# 1. 相关内容

mycat下载:

http://dl.mycat.org.cn/1.6.7.4/Mycat-server-1.6.7.4-release/

官方文档挂了,参考资料:

[mycat-definitive-guide.pdf](attachments/WEBRESOURCE1b0aa6e342dbd642250de12bc7162743mycat-definitive-guide.pdf)

# 2. 什么是MyCat

 Mycat是数据库中间件,所谓中间件,是一类连接软件组件和应用的计算机软件,以便软件各部件之间的通信。

例如 tomcat，web的中间件。而数据库中间件是连接Java应用程序和数据库中间的软件。

![](images/WEBRESOURCE6dccadbf87065a1e49ec03de816ee777.octet-stream)

# 3.为什么要用Mycat 

- Java与数据库的紧密耦合

- 高访问量高并发对数据库的压力

- 读写请求数据不一致

我们现在普遍的Java应用程序都是直接连接了MySQL软件进行读写操作，也就是我们在Java中的配置文件等定义了mysql的数据源，直接连接到了我们的mysql软件，但是当某些情况下我们可能需要用到了多个数据库，这个时候我们可能就需要配多个数据源去连接我们的多个数据库，这个时候我们进行sql操作的时候就会很麻烦，因为Java与数据库有了一个紧密的耦合度，但是如果我们在Java应用程序与mysql中间使用了mycat，我们只需要访问mycat就可以了，至于数据源等问题，mycat会直接帮我们搞定。

再来说一下高访问量高并发，我们都知道mysql数据库实际上在数据查询上是有一个瓶颈的，当我们的数据太多的时候，对于互联网上有高并发的请求的时候，这个时候对我们mysql的压力是非常大的，当访问量一大，就可能会出现查不出数据，响应的时间太长等，这个时候我们可能需要有多个服务器对数据库进行读写分离，以及对数据库进行集群，这个时候我们的sql语句要进行分类，哪个sql语句要访问哪个数据库，这个时候只要交给mycat就可以了。

![](images/WEBRESOURCE902365a1496466041a9eb319ebd3468c.octet-stream)

最后说一下，使用多个数据库的时候我们就会遇到一个读写数据不一致的问题，这个时候同样mycat可以进行主从复制，保证了数据的一致性。

# 4. mycat能干什么

## 1、读写分离

![](images/WEBRESOURCEf564032c52959a55affbb7c4bc67d125.octet-stream)

## 2、数据分片  

垂直拆分（分库）、水平拆分（分表）

首先我们的数据库有多个表

![](images/WEBRESOURCE0f257ebcfd7f252ae8fb535dd539d455.octet-stream)

当我们的表足够多的时候，也会造成整个数据库的瓶颈，这个时候查询是非常慢的，这个时候我们可能要对这个数据库进行垂直拆分，也就是分库 

![](images/WEBRESOURCE125f374e9e9af00317dc602afad3f08c.octet-stream)

我们需要垂直拆分了表4 5 6 放到另外一个库中。





当我们垂直拆分了之后，可能又会出现单个表中的数据达到千万以上，这个时候对表造成了一个瓶颈，这个时候我们对表进行拆分。

![](images/WEBRESOURCEe8d2e21a040b5f9947938feb08cd67c4.octet-stream)

我们可以把表的一部分数据拆分到另外的一个数据库。 完成: 读写分离  垂直分库   水平分表。

# 5. 原理 

Mycat 的原理中最重要的一个动词是“拦截”，

它拦截了用户发送过来的 SQL 语句，首先对 SQL  语句做了一些特定的分析：如分片分析、路由分析、读写分离分析、缓存分析等，然后将此 SQL 发 往后端的真实数据库，并将返回的结果做适当的处理，最终再返回给用户。

![](images/WEBRESOURCE786e257ccea4899ea2f649229c4d8bfe.octet-stream)

这种方式把数据库的分布式从代码中解耦出来，程序员察觉不出来后台使用 Mycat 还是 MySQL  

# 6. MyCat的安装

## 1、将压缩包上传到Linux指定的目录下并且解压

![](images/WEBRESOURCE466138625c1ff5b2f1fcecd750856b2f.octet-stream)

本次版本使用的是1.6.7.4稳定版

目录详情

![](images/WEBRESOURCEffc2080ed87276398f02aa2a14ea4e6e.octet-stream)

## 2、修改配置文件

①schema.xml：定义逻辑库，表、分片节点等内容

②rule.xml：定义分片规则

③server.xml：定义用户以及系统相关变量，如端口等 

### 2.1.设置用户

修改用户信息，与MySQL区分，如下：

![](images/WEBRESOURCE315be9138528bc62a5f9ab6fb36b68b1.octet-stream)

其中mycat、user为访问mycat的用户，TESTDB为mycat虚拟的数据库，供上层应用访问。  

### 2.2 修改schema.xml

![](images/WEBRESOURCE3626f38bf24d0e1eebe7073085e33088.octet-stream)

这里我配置了一个写主机192.168.140.128和一个读主机192.168.140.127

实现读写分离,前提是他们配置了主从复制,这部分请移步目录第7部分,搭建读写分离

这里的<dataNode>标签中 database内容 testdb是我们mysql中的数据库名称

### 2.3 请注意,如果你是mysql8.0+的用户,请看这里

作为一名良心博主,不忍心看到踩雷的情况发生,如果你是mysql8.0+的用户,那么

1.在配置schema.xml文件时,要将<dataHost>中的dbDriver链接方式更改为jdbc,且所有链接的url全部更为

```
jdbc:mysql:xxxx此处省略一万字
```

否则你会发现你可以进入mycat,但是查不到东西

2.当然,这里还需要替换一下驱动,路径在../mycat/lib中,我们需要将	mysql-connector-java-8.0.23.jar 换成与你数据库匹配的驱动,并给上777

这样才能使得mycat1.6 兼容mysql8.0+

### 2.4 启动程序

当然,首先你需要先看看我们设置的mysql有么有开启远程访问,如果没有的话,那么,你需要建立

```
grant all privileges on . to root@'%' identified by '123@qwe' 
```

mycat分为两种启动模式

1、 控制台启动 ：去 mycat/bin 目录下执行 ./mycat console

2 、后台启动 ：去 mycat/bin 目录下 ./mycat start

为了能第一时间看到启动日志，方便定位问题，我们选择1控制台启动。 

![](images/WEBRESOURCE9d29087289b14cccafde322f4ee80c1a.octet-stream)

### 2.4登录

前言:如果你发现,进入逻辑库之后的指令相当的慢,那么不用怀疑,你配置错了

#### 1、登录后台管理窗口

```
#此登录方式用于管理维护 Mycat
​mysql -umycat -pxxxxxx -P 9066 -h 192.168.56.20
#如果没有mysql命令，执行安装
yum install -y mysql
```

#### 2、登录数据窗口

```
#此登录方式用于通过 Mycat 查询数据，我们选择这种方式访问 Mycat
mysql -umycat -pxxxx -P 8066 -h 192.168.56.20
```

那么此时,你可以像使用mysql一样操作mycat了.

# 7. 搭建读写分离 (docker版本)

前言:首先,你得会docker,其次,你 必!须! 得配置数据卷.此方法同样适用于你的linux配置

第一步,我们需要启动两个mysql容器,那么命令你可以抄这里

```
#创建mysql实例 附加数据卷,以下端口号,路径均可自定义,MYSQL_ROOT_PASSWORD为mysql的root密码
#第一台msyql 名字就叫mysql01吧
docker run -d -p 3306:3306 --privileged=true 
-v /wyt/mysql/mysql01/log:/var/log/mysql 
-v /wyt/mysql/mysql01/data:/var/lib/mysql 
-v /wyt/mysql/mysql01/conf:/etc/mysql/conf.d 
-e MYSQL_ROOT_PASSWORD=123@qwe 
--name mysql01 mysql:8.0.32

#第二台msyql 名字就叫mysql02吧
docker run -d -p 3306:3306 --privileged=true 
-v /wyt/mysql/mysql02/log:/var/log/mysql 
-v /wyt/mysql/mysql02/data:/var/lib/mysql 
-v /wyt/mysql/mysql02/conf:/etc/mysql/conf.d 
-e MYSQL_ROOT_PASSWORD=123@qwe 
--name mysql01 mysql:8.0.32
```

# 8. 垂直拆分--分库 

一个数据库由很多表的构成，每个表对应着不同的业务，垂直切分是指按照业务将表进行分类， 分布到不同 的数据库上面，这样也就将数据或者说压力分担到不同的库上面，如下图

![](images/WEBRESOURCE7c2987422afcf530519193a9428637b8.octet-stream)

系统被切分成了，用户，订单交易，支付几个模块。 

## 8.1  如何划分表 

 一个问题：在两台主机上的两个数据库中的表，能否关联查询？  





 答案：不可以关联查询。 





分库的原则：有紧密关联关系的表应该在一个库里，相互没有关联关系的表可以分到不同的库里。  

```
#客户表 rows:20万
CREATE TABLE customer(
 id INT AUTO_INCREMENT,
 NAME VARCHAR(200),
 PRIMARY KEY(id)
);
#订单表 rows:600万
CREATE TABLE orders(
 id INT AUTO_INCREMENT,
 order_type INT,
 customer_id INT,
 amount DECIMAL(10,2),
 PRIMARY KEY(id) 
); 
#订单详细表 rows:600万
CREATE TABLE orders_detail(
 id INT AUTO_INCREMENT,
 detail VARCHAR(2000),
 order_id INT,
 PRIMARY KEY(id)
);
#订单状态字典表 rows:20
CREATE TABLE dict_order_type(
 id INT AUTO_INCREMENT,
 order_type VARCHAR(200),
 PRIMARY KEY(id)
);
```

 以上四个表如何分库？客户表分在一个数据库，另外三张都需要关联查询，分在另外一个数据库。 

## 8.2  实现分库

1、 修改 schema 配置文件

![](images/WEBRESOURCE03f012a3a33be353376240d83ad6ec1a.octet-stream)

去除主从关系

2、 新增两个空白库

分库操作不是在原来的老数据库上进行操作，需要准备两台机器分别安装新的数据库 

```
#在数据节点 dn1、dn2 上分别创建数据库 orders
CREATE DATABASE orders;
```

3、 启动 Mycat

```
./mycat console
```

4、 访问 Mycat 进行分库

```
#访问 Mycat 
mysql -umycat -p123456 -h 192.168.140.128 -P 8066 
#切换到 TESTDB 
#创建 4 张表 
#查看表信息，可以看到成功分 
```

![](images/WEBRESOURCEcf2e54702e704b12dfcb16b53601e7eb.octet-stream)

# 9. 水平拆分--分表

### 1、简介

相对于垂直拆分，水平拆分不是将表做分类，而是按照某个字段的某种规则来分散到多个库之中，每个表中包含一部分数据。简单来说，我们可以将数据的水平切分理解为是按照数据行的切分，就是将表中的某些行切分 到一个数据库，而另外的某些行又切分到其他的数据库中，如图：

![](images/WEBRESOURCE4830b8cf2a3562da7c31dbf150606524.octet-stream)

### 2、选择要拆分的表

MySQL 单表存储数据条数是有瓶颈的，单表达到 1000 万条数据就达到了瓶颈，会影响查询效率，需要进行水平拆分（分表）进行优化。例如：例子中的 orders、orders_detail 都已经达到 600 万行数据，需要进行分表优化。 





分表字段，以 orders 表为例，可以根据不同自字段进行分表 

![](images/WEBRESOURCE7d105deb44115f32df91277fe09d296c.octet-stream)

修改schema.xml文件,为 orders 表设置数据节点为 dn1、dn2，并指定分片规则为mod_rule（自定义的名字）: 

![](images/WEBRESOURCE4e3a17c900bbcc25b419b8c25871ac3e.octet-stream)

修改rule.xml文件，定义mod_rule规则：

![](images/WEBRESOURCEee29e9b945d924e71f925f0caa315be9.octet-stream)

![](images/WEBRESOURCE5aafd95ef69801c208fad6c0eb5feb49.octet-stream)

注意：上面我们进行了分库，现在只有spdb_pzex存在orders表，所以我们现在需要在

spdb_portal_wechat库中建立orders表 

![](images/WEBRESOURCEdf2491662bf2bf454123d2551748e43f.octet-stream)

下面我们重新启动mycat,让配置生效,orders 表插入数据，INSERT 字段不能省略：

```
INSERT INTO orders(id,order_type,customer_id,amount) VALUES (1,101,100,100100);
INSERT INTO orders(id,order_type,customer_id,amount) VALUES(2,101,100,100300);
INSERT INTO orders(id,order_type,customer_id,amount) VALUES(3,101,101,120000);
INSERT INTO orders(id,order_type,customer_id,amount) VALUES(4,101,101,103000);
INSERT INTO orders(id,order_type,customer_id,amount) VALUES(5,102,101,100400);
INSERT INTO orders(id,order_type,customer_id,amount) VALUES(6,102,100,100020);
```

在mycat、dn1、dn2中查看orders表数据，分表成功: 

![](images/WEBRESOURCEb1904ab60ae08ebb7cd1c0b972e4ef7e.octet-stream)

![](images/WEBRESOURCE9b828bdb312caf29fee3ac8777438997.octet-stream)

### 4、Mycat 的分片 “join”

  Orders 订单表已经进行分表操作了，和它关联的 orders_detail 订 单详情表如何进行 join 查询，我们要对 orders_detail 也要进行分片操作。Join 的原理如下图：

![](images/WEBRESOURCE932e03b0101f83684fcc23e0b1a3d79b.octet-stream)

使用ER表解决上面的字表关联查询的问题，其将子表的存储位置依赖于主表，并且物理上紧邻存放，因此彻底解决了JION 的效率和性能问 题，根据这一思路，提出了基于E-R 关系的数据分片策略，子表的记录与所关联的父表记录存放在同一个数据分片上。 

修改schema.xml文件： 

![](images/WEBRESOURCE1a3ff8f1ab08850cbd6697f069a1de81.octet-stream)

在spdb_portal_wechat库中创建 orders_detail 表，之前分库时，只有spdb_pzex库中有这张表 

![](images/WEBRESOURCE793cfa0474cab4a748793be8e58eec08.octet-stream)

重启 Mycat，使配置生效,

# 10. 全局表

### 1、简介

    在分片的情况下，当业务表因为规模而进行分片以后，业务与这些附属的字典表之间的关联，就成了比较 棘手的问题，考虑到字典表具有以下几个特性：

- 变动不频繁

- 数据量总体变化不大

- 数据规模不大，很少有超过数十万条记录

鉴于此，Mycat 定义了一种特殊的表，称之为“全局表”，全局表具有以下特性：

- 全局表的插入、更新操作会实时在所有节点上执行，保持各个分片的数据一致性

- 全局表的查询操作，只从一个节点获取

- 全局表可以跟任何一个表进行 JOIN 操作将字典表或者符合字典表特性的一些表定义为全局表，则从另外一个方面，很好的解决了数据JOIN 的难题。通过全局表+基于 E-R 关系的分片策略，Mycat 可以满足 80%以上的企业应用开发

### 2、修改 schema.xml 配置文件

![](images/WEBRESOURCE6d59c3ce2bd0ccfc2c773d40ce6054aa.octet-stream)

在spdb_portal_wechat库中创建 dict_order_type表，之前分库时，只有spdb_pzex库中有这张表.

![](images/WEBRESOURCE6e9ada7ceb904750d856ce2995226b66.octet-stream)

重启 Mycat，促使配置生效： 

![](images/WEBRESOURCE396694b2092607ecf66c18b1e62ddbac.octet-stream)

dict_order_type 表插入数据

```
INSERT INTO dict_order_type(id,order_type) VALUES(101,'type1');INSERT INTO dict_order_type(id,order_type) VALUES(102,'type2');
```

在以下两个库中查询数据：

![](images/WEBRESOURCEef1801e9aee56bc838439238b944e72a.octet-stream)

# 11. 常用分片规则

## 1、取模

    此规则为对分片字段求摸运算。也是水平分表最常用规则

## 2、分片枚举

    通过在配置文件中配置可能的枚举 id，自己配置分片，本规则适用于特定的场景，比如有些业务需要按照省份或区县来做保存，而全国省份区县固定的，这类业务使用本条规则。

实现方式如下： 

### 2.1、修改schema.xml配置文件 

![](images/WEBRESOURCE88a91cbcadefbe745c9c1261ff795c1c.octet-stream)

测试表为orders_ware_info，配置在dn1和dn2节点，规则是新增一个sharding_by_intfile 

## 2.2、修改rule.xml配置文件

![](images/WEBRESOURCE725c523c12edcae30e2a59e34fe5f8d7.octet-stream)

![](images/WEBRESOURCE5415d7a1e84d018c1462152fc5589a92.octet-stream)

### 2.3、修改partition-hash-int.txt配置文件

![](images/WEBRESOURCEf0ccec187a60c085cf9e7dad9597299f.octet-stream)

表示areacode为110就存到第一个数据节点，为120就存到第二个数据节点

### 2.4、重启 Mycat使配置生效

![](images/WEBRESOURCE4b40d111901ca9144af8e96e5c3f47d3.octet-stream)

使用mycat创建订单归属区域信息表： 

```
CREATE TABLE orders_ware_info
(
`id` INT AUTO_INCREMENT comment '编号',
`order_id` INT comment '订单编号',
`address` VARCHAR(200) comment '地址',
`areacode` VARCHAR(20) comment '区域编号',
PRIMARY KEY(id)
);
```

插入数据:

```
INSERT INTO ORDERS_WARE_INFO(id, order_id,address,areacode) VALUES (1,1,'北京','110');INSERT INTO ORDERS_WARE_INFO(id, order_id,address,areacode) VALUES (2,2,'天津','120');
```

验证即可.

## 3、范围约定

 此分片适用于，提前规划好分片字段某个范围属于哪个分片。

### 3.1、修改schema.xml配置文件

![](images/WEBRESOURCEc9b33b3dc63501bebf8570ec58ccff8b.octet-stream)

上面定义表以及所存储的数据节点和分片规则等

### 3.2、修改rule.xml配置文件

![](images/WEBRESOURCE28518fc3ce8e8e4c76d41227f6d4619f.octet-stream)



![](images/WEBRESOURCE26aa22441490fcf5b89638d918d840a7.octet-stream)

### 3.3、修改autopartition-long.txt配置文件

![](images/WEBRESOURCEae347756af90fea7349d9e11f0efc378.octet-stream)

### 3.4、重启 Mycat使配置生效

![](images/WEBRESOURCEc97cbe50ec4eaa825be28437656982b0.octet-stream)

在mycat中创建支付信息表payment_info：

```
CREATE TABLE payment_info( `id` INT AUTO_INCREMENT comment '编号', `order_id` INT comment '订单编号', `payment_status` INT comment '支付状态', PRIMARY KEY(id));
```

插入数据:

```
INSERT INTO PAYMENT_INFO (id,order_id,payment_status) VALUES (1,101,0);INSERT INTO PAYMENT_INFO (id,order_id,payment_status) VALUES (2,102,1);INSERT INTO PAYMENT_INFO (id,order_id ,payment_status) VALUES (3,103,0);INSERT INTO PAYMENT_INFO (id,order_id,payment_status) VALUES (4,104,1);
```

验证即可.

## 4、按日期（天）分片

此规则为按天分片。设定时间格式、范围 

### 4.1、修改schema.xml配置文件

![](images/WEBRESOURCE66717ed2a707ee27e603308a32e610a9.octet-stream)

### 4.2、修改rule.xml配置文件

![](images/WEBRESOURCE9980324aa4d5eb443c99258bcb244a4d.octet-stream)

需要自定义一个分片函数shardingByDate：  

![](images/WEBRESOURCE662b00400bf8702fc04ad4ae2c460654.octet-stream)

### 4.3、重启 Mycat使配置生效重启 

![](images/WEBRESOURCE5a23e081f6423af5b7b8db7f789973a9.octet-stream)

在mycat中创建用户信息表login_info：

```
CREATE TABLE login_info( `id` INT AUTO_INCREMENT comment '编号', `user_id` INT comment '用户编号', `login_date` date comment '登录日期', PRIMARY KEY(id));
```

插入数据:

```
INSERT INTO LOGIN_INFO(id,user_id,login_date) VALUES (1,101,'2019-01-01');INSERT INTO LOGIN_INFO(id,user_id,login_date) VALUES (2,102,'2019-01-02');INSERT INTO LOGIN_INFO(id,user_id,login_date) VALUES (3,103,'2019-01-03');INSERT INTO LOGIN_INFO(id,user_id,login_date) VALUES (4,104,'2019-01-04');INSERT INTO LOGIN_INFO(id,user_id,login_date) VALUES (5,103,'2019-01-05');INSERT INTO LOGIN_INFO(id,user_id,login_date) VALUES (6,104,'2019-01-06');
```

分别查看两个host数据节点的数据,验证即可

# 12. mycat高可用

https://blog.csdn.net/K_520_W/article/details/123781475

## 13.关于mysql -> mycat 数据迁移

![](images/WEBRESOURCE5b7d79c6dea35c2a8478e8d145830c9c.octet-stream)

```
#导出

mysql -uroot -p123456 science -N -e "select * from ai_dic_school"> 路径

```

# ===========================================================

# mycat2 部分

## 1.下载安装包

需要下载zip安装包以及jar包

zip包：http://dl.mycat.org.cn/2.0/install-template/mycat2-install-template-1.20.zip

jar包：http://dl.mycat.org.cn/2.0/1.21-release/

将jar 放入zip解压后的lib目录下

## 2.修改数据源配置

目录: mycat\conf\datasources

```
{
    "dbType":"mysql",
    "idleTimeout":60000,
    "initSqls":[],
    "initSqlsGetConnection":true,
    "instanceType":"READ_WRITE",
    "maxCon":1000,
    "maxConnectTimeout":3000,
    "maxRetryCount":5,
    "minCon":1,
    "name":"prototypeDs",
    "password":"123@qwe",
    "type":"JDBC",
    "url":"jdbc:mysql://localhost:3306/mycat?useUnicode=true&serverTimezone=Asia/Shanghai&characterEncoding=UTF-8",
    "user":"root",
    "weight":0
}
```

## 3.验证数据库链接

```
mysql -uroot -p123@qwe -h hadoop103 -P 3306
mysql -uroot -p123@qwe -h hadoop104 -P 3306
```

## 4.启动mycat

```
cd mycat/bin
./mycat start   启动
./mycat stop    停止
./mycat concole 前台运行
./mycat status  状态查看
./mycat restart 重启
./mycat pause   暂停
```

雷点

驱动文件找到但不可执行

![](images/WEBRESOURCEf5894286f86bf7cddbbbcee15e46f6e4.octet-stream)

```
chmod 755 ./wrapper-linux-x86-64 ./wrapper-linux-x86-32
```

启动完毕..

## 5.登录

1.登录管理维护mycat

```
mysql -uroot -p123@456 -P 9066
```

2.数据

```
mysql -uroot -p123456 -P 8066
```

这里的密码来源 路径: mycat/conf/users/root.user.json

```
{
    "dialect":"mysql",
    "ip":null,
    "password":"123456",
    "transactionType":"proxy",
    "username":"root"
}
```

雷区

mysql: 【Warning】 Using a password on the command line interface can be insecure.ERROR 1045 (HY000): Access denied for user 'mycat'@'192.168.10.1:63463' (using password: YES)

1.可以采用这个方式登录：

```
mysql -uroot -p123456 -P8066 -h hadoop102 -P8066 -c -A
```

2. 添加mycat用户

```
CREATE USER 'mycat'@'%' IDENTIFIED BY '123@qwe'

GRANT XA_RECOVER_ADMIN ON *.* TO 'root'@'%'

GRANT ALL PRIVILEGES ON *.* TO 'mycat'@'%'

flush PRIVILEGES

set global validate_password.policy=LOW; 
set global validate_password.length=7;
```

## 6.配置文件

### 1.服务 server

所在目录： mycat/conf/server.json

### 2.用户 user

所在目录： mycat/conf/users

命名方式-》 {用户名}.user.json

配置内容

```
{
        "dialect":"mysql",
        "ip":null,
        "password":"123456",
        "transactionType":"proxy",
        "username":"root",
        "isolation":3        
}

 #################
 #字段含义
 #ip：客户端访问ip，建议为空，填写后会对客户端ip进行限制
 #username：用户名
 #password：密码
 #isolation 设置初始化的事务隔离级别
 #READ_UNCOMMITTED:1
 #READ_COMMITTED:2
 #REPEATED_READ:3 (默认)
 #SERIALIZABLE:4
 
 #transactionType:事务类型
 # proxy 本地事务，在涉及大于一个数据库的事务commit阶段失败会导致不一致，但是兼容性最好    
 # xa事务 需要确认存储节点集群是否支持xa
 #可以通过语句实现切换
 # set transactionType_policy='proxy'
 # set transactionType_policy='xa'
 # 查询事务类型 select @@transactionType_policy
```

### 3.数据源 datasource

所在目录: mycat/conf/datasources

命名方式: {数据源名字}.datasource.json

配置内容：

```
{
        "dbType":"mysql",
        "idleTimeout":60000,
        "initSqls":[],
        "initSqlsGetConnection":true,
        "instanceType":"READ_WRITE",
        "maxCon":1000,
        "maxConnectTimeout":3000,
        "maxRetryCount":5,
        "minCon":1,
        "name":"prototypeDs",
        "password":"123@qwe",
        "type":"JDBC",
        "url":"jdbc:mysql://localhost:3306/mycat?useUnicode=true&serverTimezone=Asia/Shanghai&characterEncoding=UTF-8",
        "user":"root",
        "weight":0
}
#字段含义
#dbType  数据库类型
#name password：用户名密码
#type：数据源类型，默认JDBC
#idleTimeout： 空闲连接超时时间
#initSqls：初始化sql
#initSqlsGetConnection：对于JDBC每次获取链接是否都执行initSqls
#instanceType：配置实例是只读还是读写
#可选值：READ_WRITE,READ,WRITE
#weight:负载均衡权重
```

### 4.集群 cluster

所在目录：mycat/conf/cluster

配置内容：

```
{
    #集群类型 ：可选值 SINGLE_NODE:单一节点，MASTER_SLAVE:普通主从
    #GARELA_CLUSTER:GARELA CLUSTER/PXC集群，MHA,MGR 集群
    "clusterType":"MASTER_SLAVE",
    "heartbeat":{
        "heartbeatTimeout":1000,
        "maxRetry":3,
        "minSwitchTimeInterval":300,
        "slaveThreshold":0
    },
    #配置多个主节点，主节点挂的时候，会选择一个存活的数据源作为主节点
    "masters":[
        "prototypeDs"
    ],
    #从节点xxx
    "replicas":[
        "xxx"    
    ]
    "maxCon":200,
    "name":"prototype",
    #负载均衡策略 
    #BALANCE_ALL(默认)，获取集群中所有数据源
    #BALANCE_ALL_READ，获取集群中允许读的数据源
    #BALANCE_READ_WRITE,获取集群中允许读写的数据源，但允许读的数据源有限
    #BALANCE_NONE，获取集群中允许写的数据源，即主节点中选择
    "readBalanceType":"BALANCE_ALL",
    #切换类型
    #NOT_SWITCH:不进行主从切换
    #SWITCH：进行主从切换        
    "switchType":"SWITCH"
}
```

### 6.逻辑库表 schema

所在目录：mycat/conf/schemas

配置内容：

```

```

## 7.搭建读写分离

我们通过Mvcat.和MySQL的主从复制配合搭建数据库的读写分离，实现MySQL的高可用性。我们将搭建:一主一从、双主双从两种读写分离模式,需要配合mysql主从复制功能

目标：一个主机用于处理请求，一台从机负责所有读请求

### 1.搭建mysql主从复制

主机 hadoop102，从机hadoop103

主机配置文件：vim /etc/my.cnf

```
#主服务器唯一ID
server-id=1
#启用二进制日志
log-bin=mysql-bin
#设置不要复制的数据库（可多个）
#binlog-ignore-db=mysql
#binlog-ignore-db=information_schema
#设置需要复制的数据库需要复制的主数据库名字
binlog-do-db=mycat
#设置logbin格式
binlog_format=STATEMEN
```

从机配置：

```
#主服务器唯一ID
server-id=2
#启用中继日志
relay-log=mysql-relay
```

重启mysql

```
systemctl restart mysqld
```

查看状态是否正确

```
systemctl status mysqld
```

在主机上简历账户并授权slave

```
#在主机mysql执行授权命令
CREATE USER 'SLAVEL'@'%' IDENTIFIED BY '123@qwe'
GRANT REPLICATION SLAVE ON *.* TO 'SLAVEL'@'%'
#然后修改下密码,不然别的执行不了
ALTER USER 'SLAVEL'@'%' IDENTIFIED WITH mysql_native_password BY '123@qwe';
#刷新权限
flush privileges;
#查看信息
show master status
```

在从机配置需要复制的主机

```
CHANGE MASTER TO MASTER_HOST='主机ip',
MASTER_USER='SLAVEL',
MASTER_PASSWORD='123@qwe',
MASTER_LOG_FILE='mysql-bin.具体数字',MASTER_LOG_POS='具体'
```

配置好之后启动从服务器赋值功能

```
start slave;
```

查看从机状态

```
show slave status\G
```

雷区：

报错

![](images/WEBRESOURCE4684949a8f539dba09b4482f364fbe47.octet-stream)

```
#停止
stop slave;
#查看UUID是否相同
show variables like '%server_uuid%';

find / -name 'auto.cnf'
#将目录下的uuid随便改一位
```

如果错了，就把下面的东西跑一边，然后重新从从机配置需要复制的主机

```
#停止
stop slave;
#然后刷新
reset master
```

### 2.配置macat读写分离

#### 1.在Mycat创建逻辑库

```
#创建逻辑库
create database mydb1
#修改mydb1.schema.json指定数据源'targetName':'prototype'
#配置主机数据源
vim /opt/m/mycat/conf/schemas/mydb1.schema.json
```

链接mycat注意 端口号8066 账户密码为配置文件 mycat/conf/users
/root.user.json 中的信息

#### 2.编辑配置文件

目录：mycat/conf/schemas

文件名：{你刚才创建的逻辑库名字}.schema.json

#### 3.添加数据源

使用注解方式添加数据源，指向从机

```
#写  主机
/*+ mycat:createDataSource{ "name":"rwSepw", "url":"jdbc:mysql://hadoop101:3306/mydb1?useSSL=false&characterEncoding=UTF-8&useJDBCCompliantTimezoneshift=true","user":"root","password":"123@qwe"} */
#读  从机
/*+ mycat:createDataSource{ "name":"rwSepr", "url":"jdbc:mysql://hadoop102:3306/mydb1?useSSL=false&characterEncoding=UTF-8&useJDBCCompliantTimezoneshift=true","user":"root","password":"123@qwe"} */
#查看数据源
/*+ mycat:showDataSources{} */
```

更新集群信息，添加集群节点

```
# 创建集群，指定名字prototype，主从角色，主写从读
/*! mycat:createCluster{"name":"prototype","masters":["rwSepw"],"replicas":["rwSepr"]} */
#查看配置集群信息
/*+ mycat:showClusters{} */
#然后配置下策略 查看集群配置文件
vim ../mycat/conf/clusters/prototype.cluster.json
```

配置好了记得重启

```
./mycat restart
```

完成

## 8 验证

```sql
insert into db1 values (2,@@hostname)
```

