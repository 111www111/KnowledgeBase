大数据的导入导出工具

## 一、Sqoop介绍

Sqoop(发音：skup)是一款开源的工具，主要用于在Hadoop(Hive)与传统的数据库(mysql、postgresql...)间进行数据的传递，可以将一个关系型数据库（例如 ： MySQL ,Oracle ,Postgres等）中的数据导进到Hadoop的HDFS中，也可以将HDFS的数据导进到关系型数据库中。

Sqoop项目开始于2009年，最早是作为Hadoop的一个第三方模块存在，后来为了让使用者能够快速部署，也为了让开发人员能够更快速的迭代开发，Sqoop独立成为一个Apache项目。



## 二、安装

```
tar -zxf sqoop-1.4.6.bin__hadoop-2.0.4-alpha.tar.gz
mv sqoop-1.4.6.bin__hadoop-2.0.4-alpha sqoop1.4.6
cd /opt/installs/sqoop1.4.6/conf
mv sqoop-env-template.sh sqoop-env.sh
vi sqoop-env.sh
#增加配置，注意修改路径
export HADOOP_COMMON_HOME=/opt/installs/hadoop2.9.2
export HADOOP_MAPRED_HOME=/opt/installs/hadoop2.9.2
export ZOOCFGDIR=/opt/installs/zookeeper3.4.6
export HIVE_HOME=/opt/installs/hive1.2.1
​
将mysql的驱动jar复制到sqoop的lib目录下 (底层需要用JDBC操作MySQL数据库)
mysql-connector-java-5.1.38.jar
​
验证
bin/sqoop version
Warning: /opt/sqoop-1.4.6/bin/../../hbase does not exist! HBase imports will fail.
Please set $HBASE_HOME to the root of your HBase installation.
Warning: /opt/sqoop-1.4.6/bin/../../hcatalog does not exist! HCatalog jobs will fail.
Please set $HCAT_HOME to the root of your HCatalog installation.
Warning: /opt/sqoop-1.4.6/bin/../../accumulo does not exist! Accumulo imports will fail.
Please set $ACCUMULO_HOME to the root of your Accumulo installation.
Warning: /opt/sqoop-1.4.6/bin/../../zookeeper does not exist! Accumulo imports will fail.
Please set $ZOOKEEPER_HOME to the root of your Zookeeper installation.
20/06/25 09:12:56 INFO sqoop.Sqoop: Running Sqoop version: 1.4.6
Sqoop 1.4.6
git commit id c0c5a81723759fa575844a0a1eae8f510fa32c25
Compiled by root on Mon Apr 27 14:38:36 CST 2015
```



## 三、sqoop-import

```

-- 测试数据库的表是否可以连接，显示库中的所有表
bin/sqoop list-tables --connect jdbc:mysql://hadoop101:3306/sqoop_test --username root --password 123@qwe
```

## 导入数据

在Sqoop中，“导入”概念指：从非大数据集群（RDBMS）向大数据集群（HDFS，HIVE，HBASE）中传输数据，叫做：导入，即使用import关键字。



### 1). RDBMS -> HDFS

```
bin/sqoop import \
--connect jdbc:mysql://hadoop101:3306/sqoop_test?characterEncoding=UTF-8 \
--driver com.mysql.cj.jdbc.Driver \
--username root \
--password 123@qwe \
--table article_metadata \
--delete-target-dir \
--target-dir /mysql/article_metadata \
--num-mappers 1 \
--fields-terminated-by '\t' \
--bindir /software/sqoop/sqoop/jar
```

> -m或--num-mappers <n> 作用：启动N个map来并行导入数据，默认4个

> --fields-terminated-by <char>作用：设定每个字段是以什么符号作为结束，默认为逗号

> --target-dir <dir>作用：指定HDFS路径--delete-target-dir作用：如果目标目录已存在，则先删除

```

bin/sqoop import \
--driver com.mysql.jdbc.Driver \
--connect jdbc:mysql://hadoop10:3306/test1?characterEncoding=UTF-8 \
--username root \
--password 123456 \
--table t_user \
--columns "id,name" \
--where "id > 2" \
--target-dir /mysql/t_user \
--delete-target-dir \
--num-mappers 1 \
--fields-terminated-by '\t'
```

> --columns <col1, col2, col3>作用：指定要导入的字段--where作用：从关系数据库导入数据时的查询条件

```

bin/sqoop import \
--driver com.mysql.jdbc.Driver \
--connect jdbc:mysql://hadoop10:3306/test1 \
--username root \
--password 123456 \
--num-mappers 1 \
--fields-terminated-by '\t' \
--query 'select id, name from t_user where $CONDITIONS LIMIT 3' \
--target-dir /mysql/t_user \
--delete-target-dir
```

> --query或--e <statement>作用：将查询结果的数据导入，使用时必须伴随参--target-dir，--hive-table，如果查询中有where条件，则条件后必须加上$CONDITIONS关键字

### 2). RDBMS -> Hive

```
bin/sqoop import \
--connect jdbc:mysql://hadoop101:3306/sqoop_test \
--username root \
--password 123@qwe \
--table article_metadata \
--num-mappers 1 \
--hive-import \
--fields-terminated-by "\t" \
--hive-overwrite \
--hive-database db_hive1 \
--hive-table hive_article_metadata \
--delete-target-dir
```

> --table作用：指定关系数据库的表名

> --hive-import作用：将数据从关系数据库中导入到hive表中

> --hive-overwrite作用：覆盖掉在hive表中已经存在的数据

> --hive-table后面接要创建的hive表,默认使用MySQL的表名



该过程分为两步，第一步将数据导入到HDFS，第二步将导入到HDFS的数据迁移到Hive仓库

第一步默认的临时目录是/user/用户名/表名

## 四、sqoop-export

在Sqoop中，“导出”概念指：从大数据集群（HDFS，HIVE，HBASE）向非大数据集群（RDBMS）中传输数据，叫做：导出，即使用export关键字。

### 1). HDFS|hive -> RDBMS

① 准备数据，上传hdfs的sqoop目录下

```

vi a.txt 
# 在文件中添加如下内容
1 zhangsan true 20 2020-01-11
2 lisi false 25 2020-01-10
3 wangwu true 36 2020-01-17
4 zhaoliu false 50 1990-02-08
5 win7 true 20 1991-02-08
​
​
#在hdfs上创建sqoop目录(目录名称随意，不过需要和后边对应),将文件上传到sqoop目录下
hdfs dfs -mkidr /sqoop
hdfs dfs -put a.txt /sqoop
```

② 在mysql中创建表

> 注意：导出并不会自动创建对应的表，需要提前自己创建

```

create table t_user2(
    id int primary key auto_increment,
    name VARCHAR(32),
    sex boolean,
    age int,
    birthDay date
) CHARACTER SET=utf8;
```

③ 将hdfs上的数据导入mysql表中

```

bin/sqoop export \
--connect jdbc:mysql://hadoop10:3306/test1 \
--username root \
--password 123456 \
--table t_user2 \
--update-key id \
--update-mode allowinsert \
--export-dir /sqoop \
--input-fields-terminated-by ' '
```

> Mysql中如果表不存在，不会自动创建--export-dir                              

> 导出的数据--input-fields-terminated-by  字段分割符号导入模式可选值可以是updateonly或者allowinsert，updateonly仅仅会更新已经存在的记录。allowinsert既可以更新，又可以添加新数据









