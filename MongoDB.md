## 1. MongoDB 相关概念

### 1.1 业务场景

传统的关系型数据库 (比如 MySQL), 在数据操作的”三高”需求以及对应的 Web 2.0 网站需求面前, 会有”力不从心”的感觉

所谓的三高需求:

高并发, 高性能, 高可用, 简称三高

- High Performance: 对数据库的高并发读写的要求

- High Storage: 对海量数据的高效率存储和访问的需求

- High Scalability && High Available: 对数据的高扩展性和高可用性的需求

而 MongoDB 可以应对三高需求

具体的应用场景:

- 社交场景, 使用 MongoDB 存储存储用户信息, 以及用户发表的朋友圈信息, 通过地理位置索引实现附近的人, 地点等功能.

- 游戏场景, 使用 MongoDB 存储游戏用户信息, 用户的装备, 积分等直接以内嵌文档的形式存储, 方便查询, 高效率存储和访问.

- 物流场景, 使用 MongoDB 存储订单信息, 订单状态在运送过程中会不断更新, 以 MongoDB 内嵌数组的形式来存储, 一次查询就能将订单所有的变更读取出来.

- 物联网场景, 使用 MongoDB 存储所有接入的智能设备信息, 以及设备汇报的日志信息, 并对这些信息进行多维度的分析.

- 视频直播, 使用 MongoDB 存储用户信息, 点赞互动信息等.

这些应用场景中, 数据操作方面的共同点有:

1. 数据量大

1. 写入操作频繁

1. 价值较低的数据, 对事务性要求不高

对于这样的数据, 更适合用 MongoDB 来实现数据存储

那么我们什么时候选择 MongoDB 呢?

除了架构选型上, 除了上述三个特点之外, 还要考虑下面这些问题:

- 应用不需要事务及复杂 JOIN 支持

- 新应用, 需求会变, 数据模型无法确定, 想快速迭代开发

- 应用需要 2000 - 3000 以上的读写QPS（更高也可以）

- 应用需要 TB 甚至 PB 级别数据存储

- 应用发展迅速, 需要能快速水平扩展

- 应用要求存储的数据不丢失

- 应用需要 

99.999% 高可用

- 应用需要大量的地理位置查询, 文本查询

如果上述有1个符合, 可以考虑 MongoDB, 2个及以上的符合, 选择 MongoDB 绝不会后悔.

> 
  如果用MySQL呢?

  相对MySQL, 可以以更低的成本解决问题（包括学习, 开发, 运维等成本）


### 1.2 MongoDB 简介

> 
  MongoDB是一个开源, 高性能, 无模式的文档型数据库, 当初的设计就是用于简化开发和方便扩展, 
是NoSQL数据库产品中的一种.是最 像关系型数据库（MySQL）的非关系型数据库. 它支持的数据结构非常松散, 是一种类似于 JSON 的 ？
格式叫BSON, 所以它既可以存储比较复杂的数据类型, 又相当的灵活. MongoDB中的记录是一个文档, 
它是一个由字段和值对（ﬁeld:value）组成的数据结构.MongoDB文档类似于JSON对象, 即一个文档认 
为就是一个对象.字段的数据类型是字符型, 它的值除了使用基本的一些类型外, 还可以包括其他文档, 普通数组和文档数组.


“最像关系型数据库的 NoSQL 数据库”. MongoDB 中的记录是一个文档, 是一个 key-value pair. 字段的数据类型是字符型, 值除了使用基本的一些类型以外, 还包括其它文档, 普通数组以及文档数组

MongoDB 数据模型是面向文档的, 所谓文档就是一种类似于 JSON 的结构, 简单理解 MongoDB 这个数据库中存在的是各种各样的 JSON（BSON）

- 数据库 (database)
    

- 数据库是一个仓库, 存储集合 (collection)  

- 集合 (collection)
    

- 类似于数组, 在集合中存放文档  

- 文档 (document)
    

- 文档型数据库的最小单位, 通常情况, 我们存储和操作的内容都是文档  

在 MongoDB 中, 数据库和集合都不需要手动创建, 当我们创建文档时, 如果文档所在的集合或者数据库不存在, 则会自动创建数据库或者集合

### 数据库 (databases) 管理语法

| 操作 | 语法 |
| - | - |
| 查看所有数据库 | show dbs;<br> 或 <br>show databases; |
| 查看当前数据库 | db; |
| 切换到某数据库 (<br>若数据库不存在则创建数据库<br>) | use &lt;db\_name&gt;; |
| 删除当前数据库 | db.dropDatabase(); |


### 集合 (collection) 管理语法

| 操作 | 语法 |
| - | - |
| 查看所有集合 | show collections; |
| 创建集合 | db.createCollection("&lt;collection\_name&gt;"); |
| 删除集合 | db.&lt;collection\_name&gt;.drop() |


### 1.3. 数据模型





### 1.4 MongoDB 的特点

#### 1.4.1 高性能

MongoDB 提供高性能的数据持久化

- 嵌入式数据模型的支持减少了数据库系统上的 I/O 活动

- 索引支持更快的查询, 并且可以包含来自嵌入式文档和数组的键 (文本索引解决搜索的需求, TTL 索引解决历史数据自动过期的需求, 地理位置索引可以用于构件各种 O2O 应用)

- mmapv1, wiredtiger, mongorocks (rocksdb) in-memory 等多引擎支持满足各种场景需求

- Gridfs 解决文件存储需求

#### 1.4.2 高可用

MongoDB 的复制工具称作副本集 (replica set) 可以提供自动故障转移和数据冗余

#### 1.4.3 高扩展

水平扩展是其核心功能一部分

分片将数据分布在一组集群的机器上 (海量数据存储, 服务能力水平扩展)

MongoDB 支持基于片键创建数据区域, 在一个平衡的集群当中, MongoDB 将一个区域所覆盖的读写只定向到该区域的那些片

#### 1.4.4 其他

MongoDB支持丰富的查询语言, 支持读和写操作(CRUD), 比如数据聚合, 文本搜索和地理空间查询等. 无模式（动态模式）, 灵活的文档模型

## 2.Docker部署MongoDB

```
#下载镜像
docker pull mongo
#设置端口映射
docker run -itd --name mongo -p 8084:27017 mongo --auth
#进入
docker exec -it mongo mongo bash
5.0以上用这个
docker exec -it mongo mongosh admin
#创建管理员账号
db.createUser({ user:'admin',pwd:'123@qwe',roles:[ { role:'userAdminAnyDatabase', db: 'admin'},"readWriteAnyDatabase"]});
#登录
db.auth('admin','123@qwe');
```



![](images/WEBRESOURCE256deae3f36b429907accc7b1456b990.octet-stream)

## 3.windows部署mongodb

下载地址:https://www.mongodb.com/try/download/community

## 4.mongodb 创建用户

```
#创建用户命令
db.createUser
#用户名
user
#密码
pwd
#角色
role
```

角色权限分布

| read | 允许读取指定数据库 |
| - | - |
| readWrite | 允许读写 |
| dbAdmin | 允许用户在指定数据库执行管理函数,比如创建删除查看统计访问system.profile等 |
| userAdmin | 允许用户向system.profile写入,可以找指定数据库里创建删除管理用户 |
| root | 只在admin数据库可用 |




```
#尝试创建用户
db.createUser({"user":"root","pwd":"123456","roles":[{"role":"root","db":"admin"}]})
#查看用户
show users;
#修改密码 ,切记,密码不能有特殊字符,会有影响
db.changeUserPassword("root","123456")
#删除用户
db.dropUser("root");

```

## 5.CURD数据库

```
#创建,有了就进,没有就创建
use test
#创建后默认不会持久化,查看
show dbs
#插入一条数据,就会持久化到硬盘

#删除,在哪个库删哪个库
db.dropDatabases();
```

## 6.表(集合 collection)管理

```
#查看当前数据库集合
show tables;

#创建集合(默认)
db.createCollection("表名")

#创建集合(自定义,最大值 容量之类)
db.createCollection("表名",{"capped":true,"size":xxx})

#删除集合
db.表名.drop()
```

## 7.集合数据CURD

```
db.inventory.insertOne(
   { item: "canvas", qty: 100, tags: ["cotton"], size: { h: 28, w: 35.5, uom: "cm" } }
)

db.inventory.find( { item: "canvas" } )

db.inventory.insertMany([
   { item: "journal", qty: 25, tags: ["blank", "red"], size: { h: 14, w: 21, uom: "cm" } },
   { item: "mat", qty: 85, tags: ["gray"], size: { h: 27.9, w: 35.5, uom: "cm" } },
   { item: "mousepad", qty: 25, tags: ["gel", "blue"], size: { h: 19, w: 22.85, uom: "cm" } }
])

db.inventory.insertMany([
    { "item": "journal", "qty": 25, "size": { "h": 14, "w": 21, "uom": "cm" }, "status": "A" },
    { "item": "notebook", "qty": 50, "size": { "h": 8.5, "w": 11, "uom": "in" }, "status": "A" },
    { "item": "paper", "qty": 100, "size": { "h": 8.5, "w": 11, "uom": "in" }, "status": "D" },
    { "item": "planner", "qty": 75, "size": { "h": 22.85, "w": 30, "uom": "cm" }, "status": "D" },
    { "item": "postcard", "qty": 45, "size": { "h": 10, "w": 15.25, "uom": "cm" }, "status": "A" }
])

db.inventory.insertMany([
   { item: "journal", qty: 25, tags: ["blank", "red"], dim_cm: [ 14, 21 ] },
   { item: "notebook", qty: 50, tags: ["red", "blank"], dim_cm: [ 14, 21 ] },
   { item: "paper", qty: 100, tags: ["red", "blank", "plain"], dim_cm: [ 14, 21 ] },
   { item: "planner", qty: 75, tags: ["blank", "red"], dim_cm: [ 22.85, 30 ] },
   { item: "postcard", qty: 45, tags: ["blue"], dim_cm: [ 10, 15.25 ] }
]);


db.inventory.find({})

db.inventory.find( {"status":"D"} )
#in 
db.inventory.find({status:{$in:["A","D"]}});
#not in 
db.inventory.find({status:{$nin:["A","D"]}});
#范围
db.inventory.find({status:"A",qty:{$lt:30}});

db.inventory.find({$or:[{status:"A"},{qty:{$lt:30}}]})

//SELECT * FROM inventory WHERE status = "A" AND ( qty < 30 OR item LIKE "p%")
db.inventory.find({
    status:"A",
    $or:[
        {qty:{$lt:30}},
        {item:/^p/}
    ]
})

db.inventory.find({ size: { h: 14, w: 21, uom: "cm" } })

db.inventory.find({"size.uom":"in"})

db.inventory.find({"size.h":{$lt:15}})

//下面查询选择的所有文件在哪里嵌套的领域 h 是少于 15，嵌套的领域 uom 等于 "in"， status 领域的平等 "D":
db.inventory.find({"size.h":{$lt:15},"size.uom":"in",status:"D"})

//下面的例子查询所有文件，其中场 tags 值是一系列有两个要素， "red" 和 "blank", 在指定的顺序：
db.inventory.find({tags:{$all:["red","blank"]}})

//下面的例子查询所有文件在哪里 tags 是一个 阵列包含的字符串 "red" 作为它的一个要素：
db.inventory.find({tags:"red"})

//以下的操作中查询所有文件在哪里阵列 dim_cm 至少包含一个元件，其价值大于 25.
db.inventory.find({dim_cm:{$gt:25}})

//下面的例子查询文件在哪里 dim_cm阵列 包含的元素，在某种组合满足查询 条件；
//例如，一个元素能够满足大于 15 条件和另一个元素能够满足不到 20 条件，或一个单元能够满足这两种：
db.inventory.find( { dim_cm: { $gt: 15, $lt: 20 } } )

//例子查询文件在哪里 dim_cm阵列 至少包含一个元件是两个大于( $gt) 22 和小于( $lt) 30:
db.inventory.find( { dim_cm: { $elemMatch: { $gt: 22, $lt: 30 } } } )

//你也可以尝试锁定位置
db.inventory.find({"dim_cm.1":{ $gt: 25} })

//尝试锁定size
db.inventory.find({tags:{$size:3}})

//回到刚才,我们尝试下锁定tag的位置
db.inventory.find({"tags.0":"red"})

//查询一系列的嵌入式的文档
db.inventory.insertMany( [
   { item: "journal", instock: [ { warehouse: "A", qty: 5 }, { warehouse: "C", qty: 15 } ] },
   { item: "notebook", instock: [ { warehouse: "C", qty: 5 } ] },
   { item: "paper", instock: [ { warehouse: "A", qty: 60 }, { warehouse: "B", qty: 15 } ] },
   { item: "planner", instock: [ { warehouse: "A", qty: 40 }, { warehouse: "B", qty: 5 } ] },
   { item: "postcard", instock: [ { warehouse: "B", qty: 15 }, { warehouse: "C", qty: 35 } ] }
]);

//完全匹配
db.inventory.find( { "instock": { warehouse: "A", qty: 5 } } )
//所以 这种完全匹配,你换一下字段顺序是不能运行的
db.inventory.find( { "instock": { qty: 5, warehouse: "A" } } )
//当然,你可以使用包含函数
db.inventory.find( { "instock": { $elemMatch: { qty: 5, warehouse: "A" } } } )
//与此同时,你可以尝试锁定某个值
db.inventory.find({"instock.qty":{$lte:20}})
db.inventory.find( { "instock": { $elemMatch: { qty: { $gt: 10, $lte: 20 } } } } )

db.inventory.insertMany( [
  { item: "journal", status: "A", size: { h: 14, w: 21, uom: "cm" }, instock: [ { warehouse: "A", qty: 5 } ] },
  { item: "notebook", status: "A",  size: { h: 8.5, w: 11, uom: "in" }, instock: [ { warehouse: "C", qty: 5 } ] },
  { item: "paper", status: "D", size: { h: 8.5, w: 11, uom: "in" }, instock: [ { warehouse: "A", qty: 60 } ] },
  { item: "planner", status: "D", size: { h: 22.85, w: 30, uom: "cm" }, instock: [ { warehouse: "A", qty: 40 } ] },
  { item: "postcard", status: "A", size: { h: 10, w: 15.25, uom: "cm" }, instock: [ { warehouse: "B", qty: 15 }, { warehouse: "C", qty: 35 } ] }
]);

//限定返回字段
//SELECT _id, item, status from inventory WHERE status = "A"
db.inventory.find( { status: "A" }, { item: 1, status: 1 } )
//当然,你可以选择不要id,虽然他是默认的
db.inventory.find( { status: "A" }, { item: 1, status: 1, _id: 0 } )
//不过,如果你不想要某些字段的话,可以设置为0,像这样
db.inventory.find( { status: "A" }, { status: 0, instock: 0 } )
//你可以尝试把语句写的更规范,也可以在字段中设定返回的某个size,像这样
db.inventory.find(
   { status: "A" },
   { item: 1, status: 1, "size.uom": 1 }
)
//如果你的MongoDB的版本高于4.4,那么你可以尝试下这样嵌套的语法,他真的很nice
db.inventory.find(
   { status: "A" },
   { item: 1, status: 1, size:{"uom":1} }
)

db.students.insertMany( [
   { _id: 1, test1: 95, test2: 92, test3: 90, modified: new Date("01/05/2020") },
   { _id: 2, test1: 98, test2: 100, test3: 102, modified: new Date("01/05/2020") },
   { _id: 3, test1: 95, test2: 110, modified: new Date("01/04/2020") }
] )

db.students.find()
//指定id更新
db.students.updateOne( { _id: 3 }, [ { $set: { "test3": 98, modified: "$$NOW"} } ] )

db.students.find().pretty();

db.students.find();
db.students.updateMany({test1:{$gt:90}},{$set:{test1:100}})

db.students.updateMany(
    {test2:100},
    {$set:{test1:101}}
)

db.students.deleteOne({test1:10000})

db.students.deleteMany({_id:{$in:[1,2]}})

//针对数据中的list操作
db.inventory.insertOne(
   { title: "标题", author: ["作者1","作者2","作者3"], address: ["地址1","地址2"],author_address:["作者1:地址1"],year:2018,schoolId:[51]}
)

db.inventory.find({
    schoolId:{$in:[51]}
})

//将title为标题的schoolId添加52
db.inventory.updateOne(
   { title: "标题" },
   { $push: { schoolId: 52 } }
)

//将title为标题的schoolId的52删除
db.inventory.updateOne(
   { title: "标题" },
   { $pull: { schoolId: 52 } }
)

```

## 8.索引问题

```
//第二个参数可选 第1个参数是索引字段,并不约束这个字段是否存在,以及是否是某个类型,
//1表示升序,-1表示降序,性能无差距,考虑查询
//background 后台创建索引,不阻塞其他操作,前台创建时会只读不可写,后台不会阻塞
//name 索引名称
db.wosData.createIndex({"third_id":1},{"background":true,"name":"third_id_index"})
//推荐,但是我不买账.语法一样
db.wosData.ensureIndex()

//查看索引
db.wosData.getIndexes();

//查看索引大小 单位kb
db.wosData.totalIndexSize()

//删除全部索引
db.wosData.dropIndexes();

//删除指定索引
db.wosData.dropIndex("索引名称")
```

![](images/WEBRESOURCE4a30d517fb921ba14bb3975cda8f503f.octet-stream)

## 9.聚合,分页查询

```
#函数 aggregate()
#mongodb中 排序 分组全部在aggregate中进行
```

#### 9.1 count



![](images/WEBRESOURCE14ec020ff62183f8ca8aa93f2927d7bb.octet-stream)

统计下总计有多少行

mongo没有count函数,我们采用累加求和行数获取

```
#select count(*) as count from 表名;
db.表名.aggregate([{"$group":{"_id":null,"count":{"$sum":1}}}])
```

#### 9.2 sum

在mongo中累加直接写字段名称,相当于常量字符串.字符串不是数学类型,不能做计算

所以不能这么写!

db.students.aggregate([{"$group":{"_id":null,"count":{"$sum":"test1"}}}])

会直接返回0

```
#正确的求和计算
db.students.aggregate([{"$group":{"_id":null,"别名":{"$sum":"$字段名"}}}])
```

#### 9.3 group

同sum 直接写 "字段名" ,视为常量,所以下列的写法是错误的

db.students.aggregate([{"$group":{"_id":"字段名","count":{"$sum":"test1"}}}])

```
#正确的分组
db.students.aggregate([{"$group":{"_id":"$字段名","count":{"$sum":"test1"}}}])
```

#### 9.4 关于聚合操作的条件运用

```
#举例sql
select name,sum(AAA) as sum(BB) from table where XXX>120 group YYY having sum(BB)>999
#在mongo中,写法是这样的
db.表名.aggregate([{
    "$match": {
        "XXX": {
            "$gt": 120
        }
    }
}, {
    "$group": {
        "_id": "$YYY",
        "sum(BB)": {
            "$sum": 
            "$AAA"
        }
    }
}, {
    "$match": {
        "sum(BB)": {
            "$gt": 999
        }
    }
}])
```

## 10.JavaApi:Spring-data-mongo

### maven依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>
```

### 配置文件

```
#springdata会准备mongotemplate对象,处理远程操作
spring:
  data:
    mongodb:
      host: 127.0.0.1
      port: 27017
      #访问的数据库
      databases: test
      username: root
      #这里 一定 得 是 单引号!
      password: '123456'
      #验证用户的库
      authentication-database: admin
```

### 创建实体对象

在@Document实体创建表的时候会自动添加_class字段,为的是同一表中,存在多个类作区分使用,如果需禁用,请使用

```
@Document(disableDefaultMappedSuperclass = true)
```

```java
package com.wyt.mongo.entity;

import lombok.Data;
import org.springframework.data.annotation.Id;
import org.springframework.data.mongodb.core.mapping.Document;
import org.springframework.data.mongodb.core.mapping.Field;

import java.util.List;

/***
 * 对应集合(表)
 * Document->描述当前类和mongoDB的集合对应关系,可以省略
 * 属性 value -> 对应mongoDB集合命名,默认命名规则是类名首字母转小写.
 * 注:spring Data MongoDB 中,数据和实体对象映射处理,基于Property,需要提供get set 方法
 */
@Data
@Document("order")
public class Order {
    /**
     * 主键
     * 可以使用注解ID,表名为主键,当然 如果字段命名为id or _id 可省略
     * MongoDB中默认的主键类型是ObjectId,对应 String||ObjectId (Bson包)
     * 如果使用字符串作为ID,默认映射主键的方式为 "xxxxxxxxxxxxxx" 建议String
     */
    @Id
    private String id;
    /**
     * 如果你的实体属性名称和字段名称不匹配,需要加注解Field 描述
     */
    @Field("title")
    private String title;

    private String payment;

    private List<String> items;

}

```

#### 10.1 数据新增 insertOne

```
/**
 * insert,集成了insertOne\ insertM 你可以丢一个实体类,也可以丢一个List
 */
Order order = new Order();
order.setPayment(100.0);
order.setTitle("测试新增1");
order.setItems(Arrays.asList("商品1","商品2"));
//单个插入,返回一个新增处理后的对象.如果有自动主键生成.赋值并返回.
Order insert = mongoTemplate.insert(order);
```

#### 10.2 批量新增 insertM

```
Collection<Order> orders = Lists.newArrayList();
for (int i = 2; i < 5; i++) {
    Order o1 = new Order();
    o1.setTitle("测试新增"+i);
    o1.setPayment(100.0+i);
    o1.setItems(Arrays.asList("商品"+i));
    orders.add(o1);
}
//批量插入,返回附带主键
orders = mongoTemplate.insert(orders,Order.class);
System.out.println(orders.size());
```

#### 10.3 修改 全量替换

```
/***
 * 修改->全量替换
 * save 保存数据,
 * 如果数据不包含主键信息,则一定是新增.如果包含主键,则匹配检查相同覆盖,不同新增,不可以批量
 */
@Test
public void updateTest(){
    Order o1 = new Order();
    o1.setId("637880e98885b2080d6797b4");
    o1.setTitle("测试修改");
    o1.setPayment(520.0);
    o1.setItems(Arrays.asList("商品777"));
    mongoTemplate.save(o1);
}
```

#### 10.4 更新单条数据

```
/***
 * 修改->表达式
 * updateFirst - > db.xxx.update({},{})
 * updateFirst(Query query,Object newPojo,Class class)
 *
 * updateMulti - > db.xxx.update({},{},{"multi":true})
 */
@Test
public void updateOneTest(){
    Query query = Query.query(
            //查询条件 is in and or xxx等条件
            Criteria.where("title").is("测试新增2")
    );
    Update update = Update.update("title","测试修改updateFirst");
    UpdateResult updateResult = mongoTemplate.updateFirst(query, update, Order.class);
    System.out.println("匹配了"+updateResult.getMatchedCount()+"行数据");
    System.out.println("修改了"+updateResult.getModifiedCount()+"行数据");
}
```

#### 10.5 批量更新

```
@Test
public void updateList(){
    Query query = new Query();
    Criteria c = Criteria.where("title").in("测试新增3","测试新增4");
    query.addCriteria(c);
    Update update = new Update();
    update.set("title","这是批量修改");
    mongoTemplate.updateMulti(query,update, Order.class);
}
```

#### 10.6 主键删除 deleteById

```
/***
 * 主键删除
 * remove(Object pojo) -> 只是使用pojo中的主键作为条件,删除数据
 * remove(Class clazz) -> 删除所有匹配这个class的数据!,慎用!相当于rm -tf /*
 * remove(Query query) -> 条件删除,删除匹配数据,注意!:直接new Query()作为条件,就是删除全部
 */
@Test
public void deleteById(){
    Order order = new Order();
    //丢个主键就行了
    order.setId("63788220178d646842bdbf0d");
    DeleteResult remove = mongoTemplate.remove(order);
    //删除的行数.
    remove.getDeletedCount();
}
```

#### 10.7 条件删除

```
@Test
public void deleteByQuery(){
    Query query = Query.query(
            Criteria.where("title").is("这是删除条件")
    );
    mongoTemplate.remove(query, Order.class);
}
```

#### 10.8 查询全部

```
/***
 * 查询全部总体分为两部分
 * findAll(Class clazz)
 * -> 直接根据class获取集合名词，并返回相对应的List泛型数据
 * findAll(Class clazz , String collectionName)
 * -> 查询名称为collectionName的集合，返回class泛型的list
 */
@Test
public void testFindAll(){
    List<Order> all = mongoTemplate.findAll(Order.class);
    for (Order order:all) {
        System.out.println(order);
    }
}
```

#### 10.9 主键查询

```
/**
 * 查询主键，无数据返回null
 */
@Test
public void findOneById(){
    Order byId = mongoTemplate.findById("637880e98885b2080d6797b4", Order.class);
    System.out.println(byId);
}
```

#### 10.10条件查询

```
/**
 * 条件查询，查询单一数据，优先取第一条
 */
@Test
public void findOneByQuery(){
    Order one = mongoTemplate.findOne(Query.query(
            Criteria.where("title").is("测试修改")
    ), Order.class);
    System.out.println(one);
}

/**
 * 条件查询，等值
 */
@Test
public void findListByQuery(){
    //等值
    List<Order> orders = mongoTemplate.find(Query.query(
            Criteria.where("title").is("xxx")
    ), Order.class);
    //不等值
    List<Order> nOrders = mongoTemplate.find(Query.query(
            Criteria.where("title").ne("xxx")
    ), Order.class);

    //当然，你可以查询某个范围，就不写返回值了
    //翻译为sql 就是 where 300<=payment<=600
    mongoTemplate.find(Query.query(
            Criteria.where("payment").gte(300).lte(600)
    ), Order.class);

    //in notin
    mongoTemplate.find(Query.query(
            Criteria.where("字段").in("value1","value2")
    ),Order.class);
    mongoTemplate.find(Query.query(
            Criteria.where("字段").nin("value1","value2")
    ),Order.class);

    //exists
    //这个翻译为 where 字段 is not null，条件是f，则为is null
    mongoTemplate.find(Query.query(
            Criteria.where("字段").exists(true)
    ),Order.class);

    /**
     * 正则,这个例子是title 以测试开头
     * 此处有个问题，该语句在mongo命令行中，对应的语句为
     * db.xxx.find({"title":/^测试/})
     * /表明正则表达式的开始与结束，而在java中不需要标记范围，直接写内容即可
     */
    List<Order> title = mongoTemplate.find(Query.query(
            Criteria.where("title").regex("^测试")
    ), Order.class);
    System.out.println(title);

    /**
     * 去重，去除某一个字段的重复数据
     * <T> List<T> findDistinct(Query query, String field, Class<?> entityClass, Class<T> resultClass);
     * query 条件
     * field 去重的字段名
     * entityClass 访问集合的对应实体
     * resultClass 去重字段类型
     */
    mongoTemplate.findDistinct(new Query(), "title", Order.class, String.class);

}
```

#### 10.11多条件查询

```
@Test
public void findListByQuerys(){
    //条件这么写 and 当然 同级可以替换为or
    Query query = Query.query(
            new Criteria().andOperator(
                    Criteria.where("xxx").is("xxx"),
                    Criteria.where("yyy").is("yyy")
            )
    );
    
     //and + or嵌套
    Criteria criteria1 = new Criteria().andOperator(
            Criteria.where("title").is("xxx"),
            Criteria.where("title").is("yyy")
    );
    Criteria criteria2 = new Criteria().orOperator(
            Criteria.where("title").is("xxx"),
            Criteria.where("title").is("yyy")
    );
    Query.query(
            //两个条件and关联
            new Criteria().andOperator(
                    criteria1,criteria2
            )
    );
}
```

#### 10.12 排序，分页

```
@Test
public void sort(){
    //排序
    Query query = new Query();
    Sort sort = Sort.by(Sort.Order.desc("字段"));
    query.with(sort);

    //分页+排序,当然，排序那部分删掉就是纯分页了
    Query pageQuery = new Query();
    pageQuery.with(
            PageRequest.of(0,10,Sort.by(Sort.Order.desc("字段")))
    );
}
```

#### 10.13 聚合

```
/**
 * 聚合
 * TypedAggregation<?> aggregation, Class<O> outputType
 * aggregation
 * outputType 返回结果的类型，一般来说都是map
 */
@Test
public void agg(){
    TypedAggregation<Order> type = TypedAggregation.newAggregation(Order.class,
            Aggregation.match(Criteria.where("xxx").is("xxx")),
            Aggregation.group("title").count().as("别名")
    );
    //聚合结果
    AggregationResults<Map> aggregate = mongoTemplate.aggregate(type, Map.class);
    //返回聚合后的单行数据，如果聚合结果是多行，抛异常
    Map uniqueMappedResult = aggregate.getUniqueMappedResult();
    //返回聚合后的多行数据（0-n）
    List<Map> mappedResults = aggregate.getMappedResults();
}
```

## 11. 连接池与工具

参考

```
https://github.com/SpringForAll/spring-boot-starter-mongodb-plus/
```

sql转mongo

```
https://printlove.cn/tools/sql2mongodb
```



MongoUtils

```
/**
 * @ClassName MongoUtils.java
 * @Description mongo工具类
 * @author WangYiTong
 * @createTime 2022-11-24 08:25
 **/
@Component
public class MongoUtils {

    private static MongoTemplate mongoTemplate;

    /**
     * 静态注入
     */
    public MongoUtils (MongoTemplate mongoTemplate) {
        MongoUtils.mongoTemplate = mongoTemplate;
    }

    /**
     * 根据id查询
     * @param id
     * @param clazz
     */
    public static <T> T findOneById(String id,Class<T> clazz){
        return mongoTemplate.findById(id, clazz);
    }

    /**
     * 快捷调用,根据第三方id查询一个实例
     * @param thirdId
     * @param clazz
     * @return
     * @param <T>
     */
    public static <T> T findOneByThirdId(String thirdId,Class<T> clazz){
        Query query = Query.query(Criteria.where(EnumMongo.Third_Id.getName()).is(thirdId));
        return findOneByQuery(query,clazz);
    }

    /**
     * 通过条件查询单个实例,请确保查询条件唯一,多个结果只会返回第一个
     * @param query
     * @param clazz
     * @return
     * @param <T>
     */
    public static <T> T findOneByQuery(Query query,Class<T> clazz){
        return mongoTemplate.findOne(query, clazz);
    }

    /**
     * 根据查询条件返回一个集合
     * @param query
     * @param clazz
     * @return
     * @param <T>
     */
    public static <T> List<T> findListByQuery(Query query, Class<T> clazz){
        List<T> returnList = Lists.newArrayList();
        returnList = mongoTemplate.find(query, clazz);
        return returnList;
    }

    /**
     * 按照条件删除,返回操作条数
     * @param query
     * @param clazz
     * @return
     */
    public static Integer deleteOneByQuery(Query query, Class<T> clazz){
        Integer result = 0;
        DeleteResult remove = mongoTemplate.remove(query, clazz);
        if(remove == null){
            return result;
        }
        return Math.toIntExact(remove.getDeletedCount());
    }

    /**
     * 根据第三方id删除
     * @param thirdId
     * @param clazz
     * @return
     */
    public static Integer deleteByThirdId(String thirdId, Class<T> clazz){
        Query query = Query.query(Criteria.where(EnumMongo.Third_Id.getName()).is(thirdId));
        return deleteOneByQuery(query,clazz);
    }

    /**
     * 根据id删除
     * @param id
     * @param clazz
     * @return
     */
    public static Integer deleteById(String id, Class<T> clazz){
        Query query = Query.query(Criteria.where(EnumMongo.Id.getName()).is(id));
        return deleteOneByQuery(query,clazz);
    }

    /**
     * 根据Id批量删除
     * @param ids
     * @param clazz
     * @return
     */
    public static Integer deleteListByIds(List<String> ids,Class<T> clazz){
        Query query = Query.query(Criteria.where(EnumMongo.Id.getName()).in(ids));
        return deleteOneByQuery(query,clazz);
    }

    /**
     * 全量更新,该object必须经过Document注解修饰
     * @return
     * @param <T>
     */
    public static <T> T updateById(T object){
        //全量更新
        T save = mongoTemplate.save(object);
        return save;
    }

    /**
     * insertOrUpdate,该object必须经过Document注解修饰
     * @param object
     * @return
     * @param <T>
     */
    public static <T> T insertOrUpdate(T object){
        return mongoTemplate.save(object);
    }


    /**
     * 分页
     * @param query
     * @param clazz
     * @param page mongo分页从0开始,默认减去1
     * @param size
     * @return
     * @param <T>
     */
    public static <T> PageResult<T> pageQuery(Query query,
                                              Class<T> clazz,
                                              Integer page,
                                              Integer size){
        long count = 0;
        if (query.getQueryObject().isEmpty()) {
             count = mongoTemplate.getCollection(mongoTemplate.getCollectionName(clazz)).estimatedDocumentCount();
        }else {
             count = mongoTemplate.count(query, clazz);
        }
        query.with(
                PageRequest.of(page-1,size)
        );
        List<T> returnList = mongoTemplate.find(query, clazz);
        return ResponseUtil.successPageResult(returnList,count);
    }

    /**
     * 快速,获得一个相等的条件
     * @param key
     * @param value
     * @return
     */
    public static Query getEqualQuery(String key, Object value){
        return Query.query(Criteria.where(key).is(value));
    }

    /**
     * 多条件查询,将两个现有的条件添加一个同级并联关系
     * @param criteriaOne
     * @param criteriaTwo
     * @return
     */
    public static Query getInQuery(Criteria criteriaOne,Criteria criteriaTwo, EnumRelation enumRelation) {
        Query query = new Query();
        switch (enumRelation) {
            case OR:
                query = Query.query(
                        new Criteria().andOperator(
                                criteriaOne,criteriaTwo
                        )
                );
                break;
            case AND:
            default:
                query = Query.query(
                    new Criteria().orOperator(
                            criteriaOne,criteriaTwo
                    )
            );
            break;
        }
        return query;
    }


    /***
     * 通过iD解绑学校
     * @author WangYiTong
     * @date 2022-11-29 16:31
     */
    public static UpdateResult pullSchoolListDataById(Long id,Long schoolId,Class clazz){
        //根据ID解绑学校关系
        Query query = new Query(Criteria.where(EnumMongo.Id.getName()).is(id+""));
        //解绑学校关系
        return  pullCustomListDataByQuery(query,schoolId+"",EnumMongo.SchoolIds.getName(),clazz);
    }

    /***
     * 通过iD添加学校
     * @author WangYiTong
     * @date 2022-11-29 16:31
     */
    public static UpdateResult pushSchoolListDataById(Long id,Long schoolId,Class clazz){
        //根据ID解绑学校关系
        Query query = new Query(Criteria.where(EnumMongo.Id.getName()).is(id+""));
        //解绑学校关系
        return pushCustomListDataByQuery(query,schoolId+"",EnumMongo.SchoolIds.getName(),clazz);
    }

    /***
     * 通过iD解绑任务
     * @author WangYiTong
     * @date 2022-11-29 16:31
     */
    public static UpdateResult pullTaskListDataById(Long id,Long taskId,Class clazz){
        //根据ID解绑学校关系
        Query query = new Query(Criteria.where(EnumMongo.Id.getName()).is(id+""));
        //解绑学校关系
        return pullCustomListDataByQuery(query,taskId+"",EnumMongo.taskIds.getName(),clazz);
    }

    /***
     * 通过iD添加任务
     * @author WangYiTong
     * @date 2022-11-29 16:31
     */
    public static UpdateResult pushTaskListDataById(Long id,Long taskId,Class clazz){
        //根据ID解绑学校关系
        Query query = new Query(Criteria.where(EnumMongo.Id.getName()).is(id+""));
        //解绑学校关系
        return pushCustomListDataByQuery(query,taskId+"",EnumMongo.taskIds.getName(),clazz);
    }

    /***
     * 通过条件,添加某个list字段的数据
     * @param query 条件
     * @param data 数据
     * @param field 字段,必须是list的字段
     * @param clazz 类型
     * @return
     */
    public static UpdateResult pushCustomListDataByQuery(Query query,String data,String field,Class clazz){
        return mongoTemplate.updateFirst(query, new Update().push(field, data),clazz);
    }


    /***
     * 通过条件,去除某个list字段的数据
     * @param query 条件
     * @param data 数据
     * @param field 字段,必须是list的字段
     * @param clazz 类型
     * @return
     */
    public static UpdateResult pullCustomListDataByQuery(Query query,String data,String field,Class clazz){
        return mongoTemplate.updateFirst(query, new Update().pull(field, data),clazz);
    }

}
```

## 12. GoApi

### 12.1golang操作MongoDB-下载安装驱动并连接数据库

下载地址：

```
https://www.mongodb.com/download-center/community

```

#### 打开客户端

```
mongo.exe

```

#### 创建数据库

```
use go_db;
#创建集合
db.createCollection("student");
```

#### 下载驱动

```
go get github.com/mongodb/mongo-go-driver

```

#### 连接mongoDB

```go
package main

import (
	"context"
	"fmt"
	"go.mongodb.org/mongo-driver/mongo"
	"go.mongodb.org/mongo-driver/mongo/options"
	"log"
)


var client *mongo.Client

func initDB()  {
	// 设置客户端连接配置
	clientOptions := options.Client().ApplyURI("mongodb://localhost:27017")
	// 连接到MongoDB
	var err error
	client, err = mongo.Connect(context.TODO(), clientOptions)
	if err != nil {
		log.Fatal(err)
	}
	// 检查连接
	err = client.Ping(context.TODO(), nil)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println("Connected to MongoDB!")
}

func main() {
	initDB()
}

```

运行结果

```
Connected to MongoDB!
```

### 12.2 golang操作MongoDB-添加文档

#### 创建一个结构体

```go
type Student struct {
	Name string
	Age int
}

```



#### 添加单个文档

使用collection.InsertOne()方法插入一条文档记录：

```go
func insertOne(s Student) {
	initDB()
	collection := client.Database("go_db").Collection("student")
	insertResult, err := collection.InsertOne(context.TODO(), s)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println("Inserted a single document: ", insertResult.InsertedID)
}

```

测试

```go
func main() {
	s := Student{Name: "tom", Age: 20}
	insertOne(s)
}

```



运行结果

```
Connected to MongoDB!
Inserted a single document:  ObjectID("61124558682f5c9583330222")

```

#### 客户端查看

```
mongodb 打开客户端
use go_db
db.student.find()
db.student.remove({}) // 删除所有

```

插入多个文档

使用collection.InsertMany()方法插入多条文档记录：

```go
func insertMore(students []interface{}) {
	//students := []interface{}{s2, s3}
	initDB()
	collection := client.Database("go_db").Collection("student")
	insertManyResult, err := collection.InsertMany(context.TODO(), students)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println("Inserted multiple documents: ", insertManyResult.InsertedIDs)
}

```

测试

```go
func main() {
	s := Student{Name: "tom", Age: 20}
	s1 := Student{Name: "kite", Age: 21}
	s2 := Student{Name: "rose", Age: 22}
	students := []interface{}{s, s1, s2}
	insertMore(students)
}

```

运行结果

```
Connected to MongoDB!
Inserted multiple documents:  [ObjectID("611246c56637c3554426bc92") ObjectID("611246c56637c3554426bc93") ObjectID("611246c56637c3554426bc94")]

```

更多方法请查阅官方文档。  

### 12.3 golang操作MongoDB-查找文档

```go
package main

import (
	"context"
	"fmt"
	"log"
	"time"

	"go.mongodb.org/mongo-driver/bson"
	"go.mongodb.org/mongo-driver/mongo"
	"go.mongodb.org/mongo-driver/mongo/options"
)

var client *mongo.Client
func initDB() {
	// 设置客户端连接配置
	clientOptions := options.Client().ApplyURI("mongodb://localhost:27017")

	co := options.Client().ApplyURI("mongodb://localhost:27017")
	mongo.Connect(context.TODO(), co)
	// 连接到MongoDB
	var err error
	client, err = mongo.Connect(context.TODO(), clientOptions)
	if err != nil {
		log.Fatal(err)
	}

	client.Ping(context.TODO(), nil)
	// 检查连接
	err = client.Ping(context.TODO(), nil)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println("Connected to MongoDB!")
}

func find() {
	ctx, cancel := context.WithTimeout(context.Background(), 30*time.Second)
	defer cancel()
	collection := client.Database("go_db").Collection("student")
	cur, err := collection.Find(ctx, bson.D{})
	if err != nil {
		log.Fatal(err)
	}
	defer cur.Close(ctx)
	for cur.Next(ctx) {
		var result bson.D
		err := cur.Decode(&result)
		if err != nil {
			log.Fatal(err)
		}
		fmt.Printf("result: %v\n", result)
		fmt.Printf("result.Map(): %v\n", result.Map()["name"])
	}
	if err := cur.Err(); err != nil {
		log.Fatal(err)
	}
}



func main() {
	initDB()
	find()
}

```

### 12.4 golang操作MongoDB-更新文档

```go
package main

import (
	"context"
	"fmt"
	"log"

	"go.mongodb.org/mongo-driver/bson"
	"go.mongodb.org/mongo-driver/mongo"
	"go.mongodb.org/mongo-driver/mongo/options"
)

type Student struct {
	Name string
	Age  int
}

var client *mongo.Client

func initDb() {
	co := options.Client().ApplyURI("mongodb://localhost:27017")
	c, err := mongo.Connect(context.TODO(), co)
	if err != nil {
		log.Fatal(err)
	}

	err2 := c.Ping(context.TODO(), nil)
	if err2 != nil {
		log.Fatal(err2)
	} else {
		fmt.Println("连接成功！")
	}
	client = c
}

func update() {
	ctx := context.TODO()
	defer client.Disconnect(ctx)
	c := client.Database("go_db").Collection("Student")

	update := bson.D{{"$set", bson.D{{"Name", "big tom"}, {"Age", 22}}}}

	ur, err := c.UpdateMany(ctx, bson.D{{"name", "tom"}}, update)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Printf("ur.ModifiedCount: %v\n", ur.ModifiedCount)
}

func main() {
	initDb()
	update()
}

```

### 12.5 golang操作MongoDB-删除文档

```go
package main

import (
	"context"
	"fmt"
	"log"

	"go.mongodb.org/mongo-driver/bson"
	"go.mongodb.org/mongo-driver/mongo"
	"go.mongodb.org/mongo-driver/mongo/options"
)

type Student struct {
	Name string
	Age  int
}

var client *mongo.Client

func initDb() {
	co := options.Client().ApplyURI("mongodb://localhost:27017")
	c, err := mongo.Connect(context.TODO(), co)
	if err != nil {
		log.Fatal(err)
	}

	err2 := c.Ping(context.TODO(), nil)
	if err2 != nil {
		log.Fatal(err2)
	} else {
		fmt.Println("连接成功！")
	}
	client = c
}

func del() {

	initDb()
	c := client.Database("go_db").Collection("Student")
	ctx := context.TODO()

	dr, err := c.DeleteMany(ctx, bson.D{{"Name", "big kite"}})

	if err != nil {
		log.Fatal(err)
	}
	fmt.Printf("ur.ModifiedCount: %v\n", dr.DeletedCount)

}

func main() {
	del()
}
```

                